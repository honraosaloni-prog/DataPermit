# DataPermit

**Prepare your data for AI, without exposing what AI doesn't need.**

DataPermit is a privacy-focused tool that helps you inspect a dataset, detect
sensitive fields, apply protection rules, and produce a redacted copy that's
safe to hand to an AI tool — without ever sending the original data anywhere.

This repository contains a **working single-file web prototype** of the full
product. It is not yet the native desktop application described in the
original spec — see [Scope & Limitations](#scope--limitations) below for the
honest difference between the two.

---

## Quick start

1. Open `datapermit.html` in any modern browser (double-click it, or drag it
   into a browser window). No install, no server, no build step.
2. Click **Load Sample Dataset** to try it immediately, or **Choose File** to
   import your own CSV, XLSX, JSON, TXT, or XML file.
3. Walk through the pipeline using the left sidebar: **Overview → Data Table →
   Privacy Review → AI View → Data Quality → Verify & Report → Export.**

Everything runs in your browser tab. Nothing is uploaded, and there is no
server component — confirm this yourself by opening your browser's network
tab while you use it.

## What's implemented

The app covers all six phases of the pipeline plus project/settings management:

| Phase | What it does |
|---|---|
| **Import** | CSV, XLSX (multi-sheet), JSON, TXT (auto-detects delimiter), XML. Validates extension, size, and actual structure before loading; human-readable errors with technical details on request. Large imports (3,000+ rows) show a non-blocking "processing" indicator. |
| **Inspect** | Dataset profile (rows, columns, missing values, duplicates), a Column Inspector (click any column) with full stats, sensitivity reasoning, and sample values, and a sortable/filterable/resizable Data Table. |
| **Protect** | Per-column protocol assignment (Allow / Mask / Tokenize / Pseudonymize / Generalize / Remove / Review), plus a visual rule-builder **Protocol Library** (`WHEN … DO …`) for reusable, named protocols. A non-removable **Mandatory Security Rules** protocol always strips credentials and card numbers. |
| **Review** | Before/After comparison with search, pagination, and a guarded "reveal original values" toggle that's off by default and logged when used. |
| **Verify** | A second privacy scan that distinguishes an intentional risk (a column left Allow/Review) from a transformation failure (a masked/tokenized column that still matches a sensitive pattern). A verification checklist (row count preserved, tokenization collision-free, original file untouched via a local checksum) backs a transparent, non-black-box risk score. |
| **Export** | Protected copy as CSV/XLSX/JSON, plus optional Privacy Report, Data Quality Report, and Protocol Configuration exports. A duplicate-row decision (made in Data Quality) is respected at export time. Report can also be printed/saved as PDF. |
| **Projects** | Save a full session (dataset + protocols + protection results + audit log) locally and reopen it later. |
| **Settings** | Offline lock, dark mode, storage management, audit controls. Security toggles that would need a real backend (encryption, project passwords) are shown but honestly disabled rather than faked. |

The **AI View** — the product's signature screen — shows exactly what would
leave your device if you handed the protected file to an AI tool, with a
boundary visual and a plain-language summary of what was transformed, left
unchanged, or removed.

## Design principles carried through the build

- **Never modify the original file.** The browser's File API is read-only by
  nature, so this is structurally guaranteed, not just promised.
- **Every transformation is visible.** No silent defaults — Before/After and
  AI View exist specifically so nothing is hidden from you.
- **No fake security theater.** Settings toggles for encryption/passwords are
  shown as disabled with an honest explanation, rather than pretending to work.
- **Transparent risk scoring.** The LOW/MEDIUM/HIGH badge always comes with
  the specific factors that produced it.

## Files in this delivery

- `datapermit.html` — the application itself. Self-contained; open it directly.
  Its favicon is inlined as an SVG data URI, so it displays correctly even if
  you only keep this one file.
- `icon.svg` — the source app icon (data grid + permission-gate mark).
- `favicon.ico`, `icon-32.png`, `icon-180.png` — favicon fallbacks referenced
  by `datapermit.html`; keep them in the same folder as the HTML file.
- `icon-1024.png` and `icons/` (16 through 1024px) — the full icon set for
  desktop packaging. Feed `icon-1024.png` into `npm run tauri icon` when
  building the native app (see `ARCHITECTURE.md`) to auto-generate the
  `.icns`/`.ico`/platform icon sets Tauri needs.
- `test_datapermit.js` — an automated smoke test suite covering the core
  parsing, detection, and transformation logic. Run with `node test_datapermit.js`
  (requires Node.js; no other dependencies).
- `ARCHITECTURE.md` — how the code is organized, the state model, and how to
  extend it (including the path to a native Tauri + Python build).
- `USER_GUIDE.md` — a screen-by-screen walkthrough for end users.

## Scope & limitations

This is a **browser-based prototype**, not the native desktop application
described in the original spec. Concretely:

- **PII detection is regex + column-name heuristics**, not Presidio/spaCy.
  It's solid on structured patterns (emails, phone numbers, card numbers) and
  includes a value-based heuristic for personal names, but it is not true NER
  and will miss things a real NLP model would catch. Treat detections as a
  starting point, not a guarantee.
- **No real encryption or local database.** Projects and settings persist via
  browser `localStorage`, not SQLite. Encryption-at-rest and project
  passwords are UI-complete but intentionally disabled, since faking them
  would be worse than not having them.
- **No native installer.** This runs in any browser; it is not a
  Tauri/Electron binary. See `ARCHITECTURE.md` for what porting that would
  involve.
- **Format support**: CSV/XLSX/JSON/TXT/XML are implemented. Parquet and SQL
  database import are acknowledged in the UI as not-yet-available rather than
  silently failing.
- **Performance**: pagination keeps the UI responsive, but very large files
  (100k+ rows) will still be slower than a native backend would be, since all
  processing happens in JavaScript in a single tab.

If you need the real desktop product — Tauri shell, Python/Pandas backend,
Presidio-based detection, SQLCipher-encrypted local storage — that's a
separate build best done with a local toolchain (e.g. via Claude Code), and
`ARCHITECTURE.md` lays out how the logic in this prototype maps onto that.

##Screenshots 

<img width="1365" height="725" alt="OP1" src="https://github.com/user-attachments/assets/0885e60b-b8d2-4c40-b5c4-29580d3c153a" />
<img width="1365" height="726" alt="OP2" src="https://github.com/user-attachments/assets/559761d4-6867-4c88-83ce-4436a5f7a0b6" />
<img width="1365" height="718" alt="OP3" src="https://github.com/user-attachments/assets/c52e9c63-de3d-4ee6-9703-0bd055571247" />
<img width="1365" height="723" alt="OP4" src="https://github.com/user-attachments/assets/5aa0c70c-76c9-4298-9e46-31fcdeb52ab6" />
<img width="1365" height="546" alt="OP5" src="https://github.com/user-attachments/assets/4d732424-8413-4a9d-99f5-5f0204ef8118" />
<img width="1365" height="692" alt="OP6" src="https://github.com/user-attachments/assets/e9abb86f-c57c-4c6a-8c44-4623c818db3f" />
<img width="1364" height="711" alt="OP7" src="https://github.com/user-attachments/assets/aebc79f8-46b0-482d-adf0-42c6f2849386" />
<img width="1365" height="715" alt="OP8" src="https://github.com/user-attachments/assets/53f55ce3-d1ac-4c94-ba84-ed52c581b099" />
<img width="1365" height="721" alt="OP9" src="https://github.com/user-attachments/assets/b812f5e9-040b-48af-853c-306792c19b62" />
<img width="1365" height="659" alt="OP10" src="https://github.com/user-attachments/assets/e75d6b89-051a-4541-ba03-8310965e9120" />
<img width="1365" height="721" alt="OP11" src="https://github.com/user-attachments/assets/72174a84-30b0-40df-a0ba-52b70ccf7f5a" />
<img width="1365" height="699" alt="OP12" src="https://github.com/user-attachments/assets/050fa8d1-46b1-4865-ac11-b11683021caa" />
<img width="1365" height="688" alt="OP13" src="https://github.com/user-attachments/assets/f13a1d2b-21b7-4fae-98db-c6a640c6fb3e" />
<img width="1365" height="653" alt="OP14" src="https://github.com/user-attachments/assets/d9b94b40-ecab-405f-a14f-97594e9a42e8" />



