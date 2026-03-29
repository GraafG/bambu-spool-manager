# Google Play Store Listing — BSM

## App Name
BSM - Bambu Spool Manager

## Short Description (80 chars max)
Scan NFC tags on Bambu Lab filament spools to track your inventory.

## Full Description (4000 chars max)
BSM (Bambu Spool Manager) reads the NFC tags embedded in Bambu Lab filament spools and helps you manage your filament inventory.

Scan a spool with your phone to instantly see its filament type (PLA Basic, PLA Matte, PETG, ABS), color with official Bambu Lab color name, temperatures, weight, production date, and unique spool identifier.

Features:
- NFC scan to read Bambu Lab spool data (MIFARE Classic 1K tags)
- Automatic inventory tracking with spool IDs (#001, #002, ...)
- Status management: In Stock, In Use, Used Up
- Search and filter by filament type and color
- Cloud sync via Google sign-in (access your inventory on any device)
- Offline-first: works without internet, syncs when connected
- Used spool history for reorder reference

Companion web app at bfm-bambu-filament.web.app for dashboard view and label printing via Fichero D11s thermal printer.

Important: This app requires a phone with an NXP NFC chipset to read MIFARE Classic tags. Most Samsung phones use Broadcom NFC chips and are not compatible. Confirmed working on Pixel phones with NXP chips. Check compatibility with the free NFC TagInfo app.

Open source: github.com/GraafG/bambu-spool-manager

## Category & Tags
- **Category:** Tools
- **Tags:** 3D printing, filament, NFC, Bambu Lab, inventory, spool manager
- **Content Rating:** Everyone

## Graphics

| Asset | Size | File |
|-------|------|------|
| App icon | 512x512 PNG | `images/app-icon-512.png` |
| Feature graphic | 1024x500 PNG | `images/feature-graphic-1024x500.png` |
| Phone screenshot 1 | 1344x2992 PNG | `images/android-app-nfc-scan.png` |
| Phone screenshot 2 | 1344x2992 PNG | `images/android-app-inventory.png` |
| Phone screenshot 3 | 1344x2992 PNG | `blogpost/Screenshot_20260309-211837.png` |
| Phone screenshot 4 | 1344x2992 PNG | `blogpost/Screenshot_20260309-211844.png` |

Tablet screenshots (7-inch and 10-inch): use the same 4 phone screenshots.

## Privacy & Data Safety

- **Privacy Policy URL:** https://github.com/GraafG/bambu-spool-manager/blob/main/privacy-policy.md
- **Data collected:** NFC tag data (stored locally + cloud when signed in), Google account (name, email, profile photo for auth)
- **Data shared with third parties:** None
- **Analytics/tracking:** None
- **Data deletion:** Remove spools in-app (deletes local + cloud), or open issue at github.com/GraafG/bambu-spool-manager/issues
- **Account deletion URL:** https://github.com/GraafG/bambu-spool-manager/issues

## App Access & Account

- **Account creation:** Google sign-in (optional — app works without it)
- **Account type:** Google account (existing, not created by app)

## Contact Details

- **Email:** graafgeert@gmail.com
- **Website:** https://github.com/GraafG/bambu-spool-manager
