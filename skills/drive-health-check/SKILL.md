---
name: drive-health-check
description: Run a read-only health audit of the drive structure and write a report to the dropzone. Use monthly or when the user asks "is the drive still clean/organized?", "audit my drive", or "drive health check".
---

# Drive Health Check

Run a READ-ONLY audit of the drive root and write a short markdown report. Do NOT
move, rename, or delete anything — report only.

## Checks

1. **Root hygiene**: loose files at the drive root (only `CLAUDE.md` is allowed) and
   count of unfiled items in `00. Dropzone/` (only `README.md` allowed) and any
   browser-download folder listed in the root CLAUDE.md.
2. **Numbering**: top-level folders should match the schema in the root CLAUDE.md —
   sequential zero-padded numbers plus any `9X.` archive folders. Flag folders that
   exist but aren't in the schema, and schema entries that don't exist.
3. **New duplicates**: files matching ` (N).ext` or `- Copy` patterns modified in the
   last 45 days. Skip heavy/noisy trees: `node_modules`, `venv`/`.venv`, `_files`
   folders, photo folders, messaging-app image dumps.
4. **Credential scan**: search for `.env`, `*.pem`, `*.p12`, `*.pfx`, `*recovery*`,
   `*password*`, `*token*` outside any folder the CLAUDE.md marks as a known-legit
   home for signature/certificate files — skip node_modules/venv trees. Anything
   found is a "move to password manager" flag, never an auto-delete.
5. **Loose-file drift**: per top-level folder, count of files directly at its root;
   compare informally against the schema's expectations (some folders are flat by
   design; domain roots that should hold only subfolders should shrink over time,
   not grow).
6. **Tracker nudge**: if the owner keeps an expiry/renewals tracker (see root
   CLAUDE.md) and it still contains placeholder values, note it.

## Output

Write the report to `00. Dropzone/Drive Health Report YYYY-MM.md` (current
year-month; overwrite if it exists) with sections: Summary (3 lines max), Findings by
check, Suggested actions. Keep it under 60 lines. If everything is clean, say so in
one line per check. The next inbox-filing run will file the report away.

## Environment notes

- Cloud drives stream content — folders can look empty before their contents hydrate
  locally. Never report a folder as empty without a second check, and never suggest
  deleting anything based on one listing.
- Windows PowerShell: variables are case-insensitive (`$r` clobbers `$R`) — use
  distinct names; and always use `-LiteralPath` (real-world filenames contain
  accents and brackets).
- macOS/Linux: prefer `find`/ripgrep with quoted paths; remember macOS's default
  filesystem is case-insensitive too.
