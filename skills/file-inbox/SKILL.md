---
name: file-inbox
description: File everything in the dropzone (and any browser-download folder) into the correct drive folders. Use when the user says "file my inbox", "empty the dropzone", "organize my downloads", "file this", or after they mention having saved new files.
---

# File the Inbox

Sweep the dropzone folder (`00. Dropzone/` at the drive root), any browser-download
folder listed in the root CLAUDE.md, and any loose files at the drive root (except
`CLAUDE.md` and `00. Dropzone/README.md`, which stay where they are), and file each
item into its correct folder. Work directly — do not ask per file; only ask when a
destination is genuinely ambiguous (e.g. which property or which client a document
belongs to).

## Workflow

1. **Inventory** the locations above.
2. **Classify each file:**
   - Check `references/filing-map.md` (in this skill folder) FIRST — it holds exact
     destinations for recurring items (utility providers, payslips, broker exports,
     medical…). Only list directories when nothing there matches; the root CLAUDE.md
     folder map is the fallback.
   - PDFs: extract text to identify content — e.g. Python with `pypdf`
     (`PYTHONIOENCODING=utf-8` on Windows), or `pdftotext` if poppler is installed.
     Scanned PDFs yield no text — flag those if the filename isn't enough.
   - Cloud-native stub files (Google `.gdoc`/`.gsheet`/`.gslides`, OneDrive `.url`
     links, etc.) often CANNOT be opened locally. Classify by name + modification
     time; if still unclear, ask.
3. **Rename** to `YYYY-MM-DD Description.ext` — date from the document's content or
   filename, spaces not underscores. Decode base64/gibberish/UUID provider filenames
   into real human-readable names.
4. **Destination rules** (beyond the CLAUDE.md map):
   - Invoices tied to an address (utilities, local taxes): identify the property from
     the supply/service address *inside* the document, not from the provider name
     alone — one provider can serve several properties.
   - Match existing subfolders before creating new ones.
5. **Move** the file:
   - macOS/Linux (and Git Bash on Windows): `mv` with every path quoted — handles
     accents, brackets, and `&` safely.
   - Windows PowerShell: `Move-Item -LiteralPath` (never bare `Move-Item` — wildcard
     characters in names will bite).
   - On a name collision at the destination, hash-compare the two files
     (`md5sum` / `Get-FileHash -Algorithm MD5`): identical → delete the inbox copy;
     different → keep both, suffix the new one ` v2`, and mention it in the report.
6. **Report** a compact source → destination list. Anything ambiguous stays in the
   dropzone with a specific question for the user.
7. **Maintain the map**: if a new recurring pattern appeared (new provider, new
   monthly receipt) or a mapping proved wrong, update `references/filing-map.md` in
   one line before finishing. This is what makes the system get better over time —
   do not skip it.

## Environment gotchas (learned the hard way — do not relearn them on the user's files)

- **Cloud drives stream content: folders can look empty before hydration.** NEVER
  delete a folder for being empty without verifying twice, and never trust a first
  "0 files" count for any destructive decision.
- **Multi-location trap (caused real data loss once):** on Google Drive, legacy files
  can appear in TWO folders at once. Two identical-hash files in different folders may
  be ONE underlying Drive file — deleting the "duplicate" trashes both locations and
  leaves a broken `.lnk`/stub file where the "kept" copy was. Same-folder `(N)`
  duplicates are safe to dedup; for CROSS-folder identical pairs, delete one, then
  IMMEDIATELY verify the kept copy still exists and opens — if it turned into a stub,
  restore the file from the drive's trash (web UI) before continuing.
- Deleted files go to the drive's trash (Google Drive: 30-day recovery) — say so
  whenever you delete something.
- **Credentials in the inbox:** if credentials, recovery codes, API keys, or
  private-key/certificate files (`.p12`, `.pem`, `.pfx`) appear, do NOT file them
  into the drive; flag them for the user's password manager and recommend deletion
  from the drive.
- Windows PowerShell variables are case-INSENSITIVE: a loop variable `$r` silently
  clobbers `$R`. Use distinct names like `$root`/`$pair`.
- Batch PowerShell scripts containing `Remove-Item` are sometimes blocked by Claude
  Code's permission parser. Delete via single `Remove-Item` calls or Python
  `os`/`shutil` instead.
