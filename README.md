# Quill Sync

**enter and validate bird ringing data in the field**

*Built by ACME Research · Davidson Lab*

---

Quill Sync is a browser-based field data entry application for bird ringing and biometric studies. It runs entirely offline on a tablet or mobile device, requires no installation, and exports data as a standard CSV file compatible with downstream analysis pipelines. It was built for autumn and winter passerine ringing at mist-net stations, with real-time validation against BTO age codes, species biometric ranges, and individual bird histories.

---

## Contents

- [Features](#features)
- [Quick Start](#quick-start)
- [Field Use Guide](#field-use-guide)
- [Validation Rules](#validation-rules)
- [Data Fields](#data-fields)
- [Individuals Database](#individuals-database)
- [Species Reference](#species-reference)
- [Age Code Logic](#age-code-logic)
- [PIT Tag Scanning](#pit-tag-scanning)
- [Dual Ringer Mode](#dual-ringer-mode)
- [Exporting Data](#exporting-data)
- [Offline Use](#offline-use)
- [Technical Notes](#technical-notes)
- [Recommended Hardware](#recommended-hardware)
- [Licence](#licence)
- [Citation](#citation)

---

## Features

- **Real-time validation** — wing length, weight, age codes, sex, species, and ring number checked on every keystroke, with colour-coded flags and a summary banner
- **BTO EURING age code validation** — biologically impossible age codes flagged on retraps based on estimated hatch year and calendar year elapsed
- **Individuals database** — import your existing individuals table from a CSV, or build it live from session records; retrap birds cross-checked automatically against history
- **PIT tag barcode scanning** — rear camera scanner reads Code 128 barcodes directly into the PIT tag field; manual entry always available
- **Dual ringer mode** — two simultaneous in-progress entries, toggled at the top of the screen; each ringer's data is held while the scribe switches between them
- **Ring series shortcut** — set a prefix and last number used; the app auto-increments on each save
- **Offline-first** — no internet connection required after first load; all data stored in browser localStorage
- **CSV export** — one tap exports all session records to a UTF-8 CSV with BOM, compatible with Excel, R, and Python
- **Responsive dark theme** — designed for outdoor tablet use, high contrast, large tap targets, glove-mode friendly
- **No installation** — single self-contained HTML file; open in any modern browser

---

## Quick Start

1. **Download** `quillsync.html` from this repository
2. **Copy it to your tablet** (via USB, email, cloud sync, or any file transfer method)
3. **Open it in Chrome or Safari** — tap the file in your file manager, or open your browser and navigate to the file
4. **Optional: add to home screen** — in Chrome tap ⋮ → *Add to Home screen*; in Safari tap Share → *Add to Home Screen*. This gives a full-screen, app-like experience
5. **Set up your session** — tap the Session tab and enter your site name, date, and default location; add your ringers and assign them to the toggle bar; set your ring series prefix and last number
6. **Import your individuals database** (if you have one) — see [Individuals Database](#individuals-database)
7. **Start entering birds** on the Entry tab

> **Internet is only needed on first open** if you want the PIT tag barcode scanner to work (it loads the ZXing scanning library from a CDN). After that first load, the scanner is cached and the app runs fully offline. If you never need scanning, the app is offline from the start.

---

## Field Use Guide

### Session Setup (do this before going to the field)

On the **Session tab**:

| Field | What to enter |
|---|---|
| Site Name | Your ringing station name — appears in exports |
| Session Date | Pre-populated with today; change if needed |
| Default Location | e.g. "Mist nets, south ride" — pre-fills each entry's Location field |
| Ringers Today | Add each ringer's initials and name |
| Ringer 1 / Ringer 2 | Assign ringers to the toggle bar slots |
| Ring Prefix | The letter prefix of your current ring series, e.g. `A` |
| Last Number Used | The last ring number from your previous session |

### Entering a Bird

Fields appear in the order a ringer typically works through a bird in the hand:

1. **Initials** — select from the dropdown (pre-assigned from the toggle bar)
2. **Date / Time** — auto-stamped when the form opens; read-only
3. **Species code** — e.g. `ROBIN`, `BLUE`, `GRETI` — validated live against the species reference list
4. **Ring Type** — N (new), R (retrap), X (dead), C (change), T (colour ring)
5. **Ring Number** — auto-capitalised; validated against individuals database
6. **PIT Tag** — scan with the camera 📷 button or type manually; auto-capitalised
7. **PIT Type** — N (new tag) or R (retrap with existing tag)
8. **Age** — BTO EURING codes 1–9 and U; see [Age Code Logic](#age-code-logic)
9. **Sex** — M, F, U, or blank if not scored
10. **Sexing Method** — P (plumage), B (brood patch), C (cloacal), G (guess)
11. **Wing** — length in mm; validated against species range
12. **Weight** — in grams; validated against species range
13. **Fecal Sample** — Y or N
14. **Location** — pre-filled from session default; edit for individual net/trap
15. **Comment** — free text

Tap **Save ✓** when complete. If there are validation flags, you'll be asked to confirm before saving — you can always save anyway. The form clears and timestamps itself ready for the next bird.

### Validation Banner

A coloured banner at the top of the Entry tab shows all current flags:

- 🔴 **Red / error** — something is likely wrong (ring conflict, biometric out of range, biological impossibility, species or sex mismatch)
- 🟡 **Amber / warning** — something is unusual but may be correct (species not in reference list, age code 7/8/9, retrap ring not found in individuals)
- No banner — entry looks clean

Flags are **advisory only** — they never block entry. You can always save a flagged record.

### Switching Between Ringers

The toggle bar below the header shows both ringers. Tap either side to switch. The current ringer's in-progress data is held while you work on the other bird — species and ring number are shown in the toggle bar so the scribe always knows who is who.

---

## Validation Rules

All validation runs live on every keystroke. The following checks are performed:

| # | Check | Level | Trigger |
|---|---|---|---|
| 1 | Species code not in reference list | ⚠ Warning | Species entered but not found in species DB |
| 2 | Wing outside species range | ⛔ Error | Wing value outside min–max for that species |
| 3 | Weight outside species range | ⛔ Error | Weight value outside min–max for that species |
| 4 | Age code 7, 8, or 9 | ⚠ Warning | These codes are unusual in autumn/winter passerine ringing |
| 5 | New ring with prior record | ⛔ Error | Ring type N but ring number already in individuals database or today's records |
| 6 | Retrap ring not in individuals | ⚠ Warning | Ring type R but ring number not found in individuals database |
| 7 | Species mismatch on retrap | ⛔ Error | Entered species disagrees with individuals database record |
| 8 | Sex mismatch on retrap | ⛔ Error | Entered sex (M or F) disagrees with individuals database record (M or F); blank and U never trigger this |
| 9 | Age biologically impossible | ⛔ Error | Age code inconsistent with estimated hatch year and years elapsed since first ringing |
| 10 | PIT tag mismatch on retrap | ⛔ Error | Entered PIT tag differs from tag recorded in individuals database; dedicated "Replace PIT tag" flow available |

---

## Data Fields

The following fields are recorded per bird, and exported in this column order:

| Field | Description |
|---|---|
| `location` | Net, trap, or area within site |
| `date` | Date of capture (YYYY-MM-DD) |
| `species` | BTO species code |
| `ringNo` | BTO ring number |
| `ringType` | N / R / X / C / T |
| `age` | EURING age code |
| `sex` | M / F / U / blank |
| `sexingMethod` | P / B / C / G |
| `wing` | Maximum flattened wing length (mm) |
| `weight` | Body mass (g) |
| `fecalSample` | Y / N |
| `time` | Time of capture (HH:MM) |
| `initials` | Ringer's initials |
| `pittagNo` | PIT tag identifier (hex, capitalised) |
| `pitType` | N / R |
| `comment` | Free text |
| `site` | Site name from session settings |
| `savedAt` | Full ISO 8601 timestamp of when record was saved |
| `flagged` | ERROR / WARN / blank — validation status at time of saving |

---

## Individuals Database

The Individuals tab shows a live view of all known birds, derived from two sources merged together:

1. **Imported CSV** — your historical individuals table, generated by your post-processing pipeline (e.g. a Python script from previous seasons' data)
2. **Session records** — birds entered this session are automatically added or updated

### Importing a Historical Individuals CSV

On the **Individuals tab**, tap **Import CSV** and select your file. Column headers are matched flexibly (case-insensitive, spaces and punctuation ignored), so your Python output should be recognised automatically. The expected columns are:

| Column | Notes |
|---|---|
| Ring No | Required |
| Species | BTO species code |
| Sex | M / F / U |
| First seen | YYYY-MM-DD |
| Last seen | YYYY-MM-DD |
| No. records | Integer |
| Ring type (first) | N / R / X / C / T |
| Age (first) | EURING code |
| Hatch year (est.) | Four-digit year |
| Hatch year exact? | Yes / No / Y / N / True / 1 |
| First location | Free text |
| PIT tag | Hex string |

Missing columns are skipped gracefully. Rows with no ring number are skipped. Importing again merges new data with existing — existing records are updated, new rings are added.

### Derived Fields

The following fields in the Individuals view are **calculated automatically** and never stored separately, eliminating any risk of the individuals table diverging from the records:

- **First seen** — date of earliest record for that ring number
- **Last seen** — date of most recent record
- **No. records** — count of all records for that ring number

---

## Species Reference

Quill Sync ships with biometric ranges for 46 common British passerine and near-passerine species. These are used for wing and weight validation.

To load the defaults, go to **Session → Species Reference → Load Defaults**.

You can add or edit species at any time using the form on the Session tab. Custom species are stored locally and persist across sessions.

### Default species included

Blue Tit, Blackbird, Blackcap, Bullfinch, Chaffinch, Chiffchaff, Common Redpoll, Common Whitethroat, Dunnock, Fieldfare, Firecrest, Garden Warbler, Goldcrest, Goldfinch, Great Spotted Woodpecker, Great Tit, Greenfinch, House Sparrow, Lesser Redpoll, Lesser Whitethroat, Linnet, Long-tailed Tit, Marsh Tit, Mistle Thrush, Nuthatch, Pied Flycatcher, Reed Bunting, Reed Warbler, Redstart, Redwing, Robin, Sedge Warbler, Siskin, Song Thrush, Spotted Flycatcher, Stonechat, Swift, Treecreeper, Tree Sparrow, Wheatear, Whinchat, Willow Tit, Willow Warbler, Wood Warbler, Wren, Yellowhammer.

> **Note:** Biometric ranges are approximate field guides and should be reviewed against your own population data. They can be edited freely in the Species Reference panel.

---

## Age Code Logic

Quill Sync uses the BTO/EURING age code system. On retrap entries, the app estimates the bird's hatch year from its first-ring age code and first-ring date, then checks whether the entered age code is biologically consistent with the number of calendar years elapsed.

### EURING age codes

| Code | Description |
|---|---|
| 1 | Pullus — nestling in the nest |
| 2 | Full grown, age unknown |
| 3 | Juvenile — in full juvenile plumage, hatched this calendar year |
| 4 | Ringed as juvenile or 1st year, now older |
| 5 | First year — hatched this calendar year, post-juvenile moult begun or complete |
| 6 | First year or older — cannot distinguish |
| 7 | Second year — hatched last calendar year |
| 8 | Second year or older |
| 9 | Adult — at least 2 years old |
| U | Unknown |

### Retrap age validation

| Years elapsed since hatch year | Valid codes | Suggested code |
|---|---|---|
| 0 (same calendar year) | 1, 2, 3, 4, 5, 6, U | 5 |
| 1 (one calendar year later) | 2, 4, 6, 7, 8, U | 7 (if hatch year exact) or 8 |
| 2+ (two or more calendar years later) | 2, 4, 6, 8, 9, U | 9 |

Age code 2 (full grown, age unknown) is always valid — it is never flagged on a retrap. Codes 7, 8, and 9 on any entry trigger a soft warning, as these are rarely used in autumn/winter passerine ringing contexts. The warning does not block saving.

---

## PIT Tag Scanning

The 📷 button on the PIT Tag field opens a camera overlay that scans **Code 128** barcodes in real time. The decoded string is automatically capitalised and entered into the PIT tag field. The camera overlay closes as soon as a code is detected.

**Requirements:**
- The device must have a rear-facing camera
- The browser must be granted camera permission (you'll be asked on first use)
- The ZXing library must have been loaded at least once with an internet connection (it is then cached for offline use)

**Tips for reliable scanning:**
- Ensure adequate lighting — the device torch can help in dark conditions
- PIT tag barcodes are often small; hold the device 10–20 cm from the tag
- If the camera struggles to focus, a clip-on macro lens (widely available, inexpensive) helps significantly
- Manual entry is always available as a fallback

**PIT tag conflict:** If a retrap bird already has a different PIT tag on record, Quill Sync flags this as an error and opens a dedicated confirmation dialogue. You can either go back and correct a scan error, or confirm a genuine tag replacement — which updates the individuals database.

---

## Dual Ringer Mode

The toggle bar below the header shows two ringer slots. Each slot displays the ringer's initials and their current in-progress species and ring number.

Tapping a slot switches the Entry form to that ringer's data. The other ringer's in-progress entry is held in memory — nothing is lost when switching. After saving a record, the form clears and is ready for that ringer's next bird, while the other ringer's parked data remains intact.

**Setting up the toggle bar:**
1. Go to Session → Ringers Today and add each ringer's initials and name
2. Under "Assign to toggle bar", select which ringer goes in slot 1 (blue) and slot 2 (orange)
3. The assigned initials will be pre-filled in each new entry's Initials field automatically

---

## Exporting Data

Every time an entry is saved with **Save ✓**, Quill Sync immediately downloads a CSV containing that single entry. The file is named:

```
entry_save_YYYY-MM-DD-HH-MM-SS.csv
```

This provides an individual backup for each saved record. To export all records currently stored for the session, open the **Records** tab and tap **⬇ Export CSV**. The file is named:

```
Quill_Sync_Records_YYYY-MM-DD-HH-MM-SS.csv
```

Both CSV types are UTF-8 encoded with a byte order mark (BOM) for correct Excel compatibility. The session export contains all records currently stored in the app, including a `flagged` column showing ERROR, WARN, or blank for each record.

The browser chooses the download folder; the HTML file cannot specify an arbitrary filesystem path. Set the browser to ask where to save downloads, or configure a dedicated folder such as `Quill Sync/Entry Saves` and `Quill Sync/Session Exports`. Check that the file appears after each save and copy exports to a second device or drive when possible.

> **Tip:** Export the complete session at the end of every field session and after any significant batch of birds. Individual entry downloads are an additional backup, not a replacement for keeping a copy of the complete session export.

---

## Offline Use

Quill Sync is designed to work without internet access in the field. All data is stored in your browser's `localStorage` — it persists when you close the browser or turn the device off, and is recovered automatically when you reopen the file.

**To ensure full offline capability:**
1. Open `quillsync.html` once with internet access (this caches the ZXing scanner library from the CDN)
2. After that, the app and scanner work with no connectivity

**Alternatively**, for a fully self-contained version with no CDN dependency at all (the ZXing library inlined directly into the HTML file), open an issue or contact the Davidson Lab — this version is available on request and is recommended for field sites with no prior connectivity.

---

## Technical Notes

- **No server, no database, no accounts** — Quill Sync is a single static HTML file
- **Storage:** browser `localStorage`, scoped to the file's origin. Approximately 5MB limit — sufficient for thousands of records per session
- **Compatibility:** tested in Chrome (Android and desktop) and Safari (iOS). Any modern browser from 2020 onwards should work
- **Data persistence:** data survives browser restarts but will be lost if you clear browser site data. Export your CSV at the end of every session
- **Multiple devices:** data does not sync between devices. Each device maintains its own independent record store. If two scribes are working on separate tablets, export and merge CSVs afterwards
- **ZXing barcode library:** loaded from `cdnjs.cloudflare.com` on first use. Version pinned to 0.21.3 for stability
- **File size:** approximately 150KB including the embedded logo

---

## Recommended Hardware

Quill Sync works on any Android or iOS tablet/laptop with a modern browser. For field use, we recommend:

**Minimum spec:**
- Android tablet with 4GB RAM
- Snapdragon 680 / MediaTek Helio G99 or equivalent processor
- 8–10" screen
- USB-C with OTG support
- 5000mAh+ battery

---

## Licence

This work is licensed under a [Creative Commons Attribution-NonCommercial 4.0 International Licence (CC BY-NC 4.0)](https://creativecommons.org/licenses/by-nc/4.0/).

**You are free to:**
- Use, share, and adapt Quill Sync for your own field research
- Translate or modify it for different ringing schemes or species sets

**Under the following terms:**
- **Attribution** — You must give appropriate credit to the Davidson Lab and ACME Research, provide a link to this repository, and indicate if changes were made
- **NonCommercial** — You may not use this work for commercial purposes

[![CC BY-NC 4.0](https://licensebuttons.net/l/by-nc/4.0/88x31.png)](https://creativecommons.org/licenses/by-nc/4.0/)

---

## Citation

If you use Quill Sync in your research, please cite it as:

> Davidson Lab, ACME Research (2025). *Quill Sync: field biometric data capture for bird ringing studies* [Software]. Available at: https://github.com/[your-username]/quillsync

A BibTeX entry is available in `CITATION.bib`.

---


*Quill Sync is built by ACME Research (Animal Cognition and Microbial Ecology), Davidson Lab.*
