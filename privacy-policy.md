# Privacy Policy — BSM (Bambu Spool Manager)

**Last updated: March 10, 2026**

## Overview
BSM (Bambu Spool Manager) is an open-source Android app for managing Bambu Lab 3D printer filament spools. This policy explains what data the app collects and how it is used.

## Data Collection

### NFC Tag Data
The app reads data from NFC tags embedded in Bambu Lab filament spools. This data includes filament type, color, weight, temperatures, production date, and a spool identifier. This data is stored locally on your device and optionally synced to the cloud.

### Google Account
If you sign in with Google, the app accesses your name, email address, and profile photo solely for authentication and to sync your inventory across devices. Your Google credentials are handled entirely by Google's sign-in service and are never stored by the app.

### Cloud Storage
When signed in, your spool inventory data is stored in Google Firebase Firestore. This data is associated with your Google account and is only accessible by you. Data is stored in Google's cloud infrastructure (see Google's privacy policy for details).

### No Analytics or Tracking
The app does not collect analytics, usage data, crash reports, or any form of tracking data. No data is shared with third parties.

## Data Storage
- **Local**: Spool data is stored in a local database on your device using Android Room.
- **Cloud**: When signed in, data syncs to Firebase Firestore. You can delete your cloud data at any time by removing all spools from your inventory.

## Data Deletion

### Delete your data (without deleting your account)
You can delete your spool inventory data at any time:
1. Open the app and remove individual spools, or remove all spools from your inventory
2. This deletes the spool data from both your local device and Firebase Firestore immediately

### Delete your account and all associated data
To fully delete your account and all data:
1. Remove all spools from your inventory in the app (deletes cloud data immediately)
2. Sign out of the app (disconnects your Google account)
3. Uninstall the app (removes all local data including the Room database)
4. Optionally, revoke BSM's access to your Google account at https://myaccount.google.com/permissions

**What gets deleted:**
- All spool inventory records (local and cloud)
- Your authentication session

**What is NOT stored and therefore does not need deletion:**
- We do not store your Google password, profile photo, or any personal data beyond what is listed above
- No analytics, logs, or tracking data exist to delete

To request account deletion via email, open an issue at https://github.com/GraafG/bambu-spool-manager/issues and we will delete your Firestore data within 7 days.

## Children's Privacy
This app is not directed at children under 13 and does not knowingly collect data from children.

## Open Source
This app is open source. You can review the complete source code at: https://github.com/GraafG/bambu-spool-manager

## Contact
For questions about this privacy policy, open an issue on GitHub: https://github.com/GraafG/bambu-spool-manager/issues

## Changes
We may update this policy from time to time. Changes will be posted in the app's GitHub repository.
