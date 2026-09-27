# Changelog

All notable changes to Quill Sync are documented here.

## [Unreleased] - 2026-09-27

### Added

- Automatic per-entry CSV backup after every successful save.
- Entry backup filenames using the format `entry_save_YYYY-MM-DD-HH-MM-SS.csv`.
- Timestamped full-session CSV export from the Records tab.
- Session export filenames using the format `Quill_Sync_Records_YYYY-MM-DD-HH-MM-SS.csv`.

### Changed

- CSV generation now uses a shared download helper for individual-entry and full-session exports.
- README documentation now explains local browser storage, CSV backups, browser-controlled download locations, and recommended field backup procedures.

### Notes

- Records are stored locally in browser `localStorage` and remain available offline.
- The browser controls the download folder; the application cannot choose an arbitrary filesystem path.
- Session exports should still be copied to a second device or drive when possible.
