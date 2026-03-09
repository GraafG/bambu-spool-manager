# BSM — Bambu Spool Manager

An Android + web app that reads NFC tags on Bambu Lab filament spools and manages your filament inventory. Scan a spool to see its type, color, temperatures, and track stock across printers. Print labels directly from the web app via a Fichero D11s thermal printer.

## Features

### Android App
- **NFC Scan** — Read Bambu Lab spool NFC tags (MIFARE Classic 1K) to get filament type, color, temperatures, weight, and production date
- **Inventory Management** — Track each spool individually with statuses: In Stock, In Use (in printer), Used Up
- **Spool IDs** — Each spool gets a short ID (#001, #002) for easy physical identification
- **Used Spool History** — Keep a record of used-up spools for reorder reference
- **Search & Filter** — Find spools by type, color, or notes
- **Cloud Sync** — Firebase Firestore with offline-first sync and Google sign-in

### Web App
- **Inventory Dashboard** — View all spools in card grid or sortable table view
- **Filter & Search** — Filter by filament type (PLA Basic, PLA Matte, PETG), search by color name, hex code, or notes
- **Color Names** — Official Bambu Lab color names resolved from hex codes (Cyan, Marine Blue, Ice Blue, etc.)
- **Label Printing** — Print labels via [Fichero D11s](https://www.action.com/nl-nl/p/3212141/fichero-labelprinter/) thermal printer using Web Bluetooth
- **Batch Print** — Print labels for all inventory spools at once
- **Label Format** — Filament type, color name, spool ID + hex code (232x96px, 30x14mm stickers)
- **Live at** [bfm-bambu-filament.web.app](https://bfm-bambu-filament.web.app)

## Phone Compatibility

This app requires a phone with an **NXP NFC chipset** to read MIFARE Classic tags. Most phones use either NXP or Broadcom NFC chips — only NXP supports MIFARE Classic.

**Confirmed working:**
- Pixel 10 Pro XL

**Known NOT to work:**
- Most Samsung Galaxy phones (Broadcom NFC)
- Some older Pixel phones

You can check your phone's compatibility by installing [NFC TagInfo](https://play.google.com/store/apps/details?id=at.mroland.android.apps.nfctaginfo) and scanning a Bambu spool. If it shows "MIFARE Classic" in the tech list, your phone is compatible.

## Setup

### Android App

#### Prerequisites
- Android Studio (latest stable)
- JDK 17+
- An Android phone with NXP NFC chip
- A Bambu Lab filament spool with NFC tag

#### 1. Clone the repo
```bash
git clone https://github.com/geertdegraaf/bambu-spool-manager.git
cd bambu-spool-manager
```

#### 2. Firebase Setup

Create a Firebase project and register an Android app:
```bash
npm install -g firebase-tools
firebase login
firebase projects:create your-project-id --display-name "BSM"
firebase apps:create android --project your-project-id --package-name com.bambu.nfc
firebase apps:sdkconfig android --project your-project-id --out app/google-services.json
```

Enable **Authentication** (Google sign-in) and **Firestore** in the Firebase console.

> **Note:** `google-services.json` is gitignored. See `app/google-services.json.example` for the expected format.

#### 3. Build & Run
```bash
./gradlew assembleDebug
./gradlew installDebug
```

#### 4. Enable NFC
Settings > Connected devices > Connection preferences > NFC

### Web App

#### Prerequisites
- Node.js 18+
- Firebase CLI (`npm install -g firebase-tools`)

#### 1. Firebase Hosting Setup

```bash
cd web
firebase login
firebase use your-project-id
```

Enable **Firestore** and **Authentication** (Google sign-in) in the Firebase console, then configure your web app client ID in `web/public/index.html`.

#### 2. Fichero Printer Library (optional)

The label printing feature uses the [fichero-printer](https://github.com/0xMH/fichero-printer) library. To rebuild it from source:

```bash
# Clone the Fichero library (one-time)
git clone https://github.com/0xMH/fichero-printer.git lib/fichero-printer

# Bundle TypeScript to ES module
cd web
bash build-fichero.sh
```

The bundled `web/public/fichero.js` is included in the repo so this step is only needed when updating from upstream.

To update when the Fichero repo has changes:
```bash
cd lib/fichero-printer && git pull
cd ../../web && bash build-fichero.sh
```

#### 3. Deploy
```bash
cd web
firebase deploy --only hosting
```

#### 4. Printer Hardware

Label printing requires a **Fichero D11s** thermal label printer (available at [Action](https://www.action.com/nl-nl/p/3212141/fichero-labelprinter/) for ~20 euro) with 30x14mm sticker labels. The web app connects via Web Bluetooth (Chrome/Edge/Opera).

## How It Works

### NFC Tag Format
Bambu Lab spools use **MIFARE Classic 1K** NFC tags (NXP MF1S50, 13.56 MHz). The tag has 16 sectors with 4 blocks each (16 bytes/block).

Authentication uses **HKDF-SHA256** key derivation:
- The 4-byte tag UID is used as input key material
- Salt: `9a759cf2c4f7caff222cb9769b41bc96`
- Info: `"RFID-A\0"` (with null terminator)
- A single HKDF expand produces 96 bytes, split into 16 keys of 6 bytes each (one per sector)

Data is stored across these blocks:

| Block | Content |
|-------|---------|
| 1 | Material ID |
| 2 | Filament type (e.g., "PLA", "PETG") |
| 4 | Detailed type (e.g., "PLA Basic", "PLA Matte") |
| 5 | Color (RGBA), weight (g), diameter (mm) |
| 6 | Temperatures (hotend min/max, bed) |
| 9 | Tray UID (unique identifier per spool) |
| 12 | Production date |
| 14 | Filament length (m) |

### Label Printing
The web app renders labels on a 232x96px canvas (30x14mm at 203 DPI). The Fichero D11s has a 96px printhead with "left" print direction — the canvas is rotated 90 degrees before rasterizing to 1-bit monochrome. Anti-aliased text is thresholded to pure black/white before encoding.

### Inventory Model
Each physical spool is tracked individually with a status:

```
IN_STOCK  →  IN_USE  →  USED_UP
(on shelf)   (in printer)  (empty, kept for history)
```

## Architecture

```
Android App:
  MVVM + Clean Architecture
  UI (Compose) → ViewModel (StateFlow) → UseCase → Repository → Room DB
                                                                → Firestore
  NFC Tag → BambuTagReader → BambuTagParser → FilamentData

Web App:
  Single-page HTML + Firebase SDK + Fichero printer library
  Firebase Auth (Google) → Firestore → Real-time inventory UI
  Canvas rendering → ImageEncoder → Web Bluetooth → Fichero D11s
```

- **Kotlin** with coroutines and Flow
- **Jetpack Compose** + Material 3
- **Hilt** for dependency injection
- **Room** for local database
- **Firebase Firestore** for cloud sync (offline-first)
- **Web Bluetooth** for printer communication

## Project Structure

```
app/src/main/java/com/bambu/nfc/
├── data/
│   ├── local/          # Room database (entity, dao, AppDatabase)
│   ├── nfc/            # NFC reading (KeyDerivation, BambuTagReader, BambuTagParser)
│   ├── firestore/      # Firebase Firestore sync
│   └── repository/     # SpoolRepository implementation
├── di/                 # Hilt modules
├── domain/
│   ├── model/          # Spool, FilamentData, SpoolStatus
│   └── usecase/        # ProcessScannedTagUseCase
├── ui/
│   ├── components/     # Shared composables (ColorSwatch, StatusBadge)
│   ├── detail/         # Spool detail screen
│   ├── inventory/      # Inventory list screen
│   ├── scan/           # NFC scan screen
│   └── theme/          # Material 3 theme
├── util/               # Extensions (ByteArray helpers, color conversion)
└── MainActivity.kt     # NFC foreground dispatch, navigation host

web/
├── public/
│   ├── index.html      # Single-page web app
│   └── fichero.js      # Bundled Fichero printer library
├── build-fichero.sh    # Script to rebuild fichero.js from TypeScript source
├── firebase.json       # Firebase hosting config
└── firestore.rules     # Firestore security rules

lib/
└── fichero-printer/    # Cloned from github.com/0xMH/fichero-printer (not committed)
```

## Contributing

Contributions welcome! Please open an issue first to discuss what you'd like to change.

## License

This project is licensed under the [MIT License](LICENSE).

## Acknowledgments

- [fichero-printer](https://github.com/0xMH/fichero-printer) by [@0xMH](https://github.com/0xMH) — Web Bluetooth library for the Fichero D11s thermal label printer, made the label printing integration possible
- [Fichero D11s label printer](https://www.action.com/nl-nl/p/3212141/fichero-labelprinter/) — Budget thermal printer from Action (~20 euro)
- [Bambu Lab color hex codes](https://printbusters.io/filament-colors/bambu-lab) — Official Bambu Lab filament color reference
- NFC tag format research inspired by [MifareClassicTool](https://github.com/ikarus23/MifareClassicTool)
- HKDF key derivation based on community reverse-engineering of Bambu Lab's RFID authentication
- Built with [Claude Code](https://claude.com/claude-code)
