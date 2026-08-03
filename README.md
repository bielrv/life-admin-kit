# life-admin-kit

**Turn your Google Drive (or any file tree) into an AI-managed filing system.**

You save files into one folder. Claude files everything: renames each document to a
consistent convention, reads PDFs to figure out what they are, routes them to the right
folder, and remembers the pattern for next time. Your file tree stays clean without you
ever thinking about it again.

Built from a real system that has been filing one person's entire life admin — tax
returns, utility invoices, rental property paperwork, payslips, medical records, travel
bookings — for months, hands-off. This kit is the genericized, battle-tested version.
The full story, including the day it destroyed data and the guardrails that came out of
it: [I gave Claude Code my Google Drive](https://bielrv.substack.com/p/i-gave-claude-code-my-google-drive).

## The idea in one diagram

```
  You (or your browser) drop files here
                 │
                 ▼
        ┌─────────────────┐
        │  00. Dropzone/  │   ← the only folder you ever touch
        └────────┬────────┘
                 │  "file my inbox"
                 ▼
        ┌─────────────────┐      ┌──────────────────────┐
        │   file-inbox    │─────▶│  filing-map.md        │
        │   (skill)       │◀─────│  (learned state)      │
        └────────┬────────┘      └──────────────────────┘
                 │  rename → YYYY-MM-DD Description.ext
                 ▼
   01. Documents/   03. Property/   05. Investments/   ...
```

## Philosophy

Four pieces, each doing one job:

1. **CLAUDE.md is the schema.** A single file at your Drive root describes your folder
   structure, naming conventions, and filing rules. Claude reads it at the start of
   every session. Change the file, change the system.
2. **The dropzone is the API.** One inbox folder (`00. Dropzone/`) is the only write
   interface you need. Save anything there with any filename — the AI does the rest.
3. **Skills are the automations.** Small, focused instruction files: one that files the
   inbox, one that audits drive health, one that sets the whole thing up by
   interviewing you.
4. **The filing map is learned state.** A markdown table of recurring patterns
   ("invoice from Acme Energy → Property A/Utilities/Electricity"). The filing skill
   consults it first and *updates it itself* when a new pattern appears or an old one
   proves wrong. The system gets faster and more accurate the longer you use it.

No database, no app, no sync service. Just markdown files and an agent that reads them.

## Quick start

1. Install [Claude Code](https://claude.com/claude-code) and make your cloud drive
   available as a local folder (Google Drive for desktop, OneDrive, Dropbox, iCloud
   Drive, Syncthing — anything that shows up as a file tree works).
2. Install this kit (see [INSTALL.md](INSTALL.md) — as a plugin or by copying the
   `skills/` folders into `.claude/skills/` at your drive root).
3. Open Claude Code **at your drive root** and run the setup interview:
   ```
   /setup-drive
   ```
   It asks about your life domains (property? freelance? investments? which country's
   tax paperwork?), then generates a personalized `CLAUDE.md` schema and a seed
   `filing-map.md`, and creates the folder skeleton.
4. Save a few files into `00. Dropzone/` and say:
   ```
   file my inbox
   ```
5. Once a month:
   ```
   run a drive health check
   ```

## What's in the box

| Path | What it is |
|------|------------|
| `templates/CLAUDE.md.template` | Generic starter schema: numbered folders, naming convention, dropzone workflow, archive convention |
| `skills/setup-drive/` | Interviews you and generates your personalized CLAUDE.md + seed filing map |
| `skills/file-inbox/` | The workhorse: sweeps the dropzone, classifies, renames, files, and maintains the filing map |
| `skills/file-inbox/references/filing-map.md` | Template learned-state table with example rows |
| `skills/drive-health-check/` | Read-only monthly audit: root hygiene, duplicates, credential scan, drift |

## Safety notes — read these

This kit moves and renames your files. It was tuned on real data and encodes several
hard-won lessons; the skills enforce them, but you should know them too:

- **Cloud drives lie about emptiness.** Streaming clients (Google Drive for desktop
  especially) show folders as empty before their contents have hydrated locally. The
  skills never trust a first "0 files" count for any destructive decision.
- **The cross-folder duplicate trap.** On Google Drive, legacy files can live in two
  folders at once as *one* underlying file. Deleting the "duplicate" can trash both
  locations, leaving a broken `.lnk` stub where the "kept" copy was. The file-inbox
  skill dedups same-folder `(1)` copies freely but treats cross-folder identical pairs
  with a verify-after-delete protocol.
- **Nothing is hard-deleted.** Deletions go to your drive's trash (Google Drive: 30-day
  recovery window), and the skills say so whenever they delete.
- **Collisions are hash-compared.** A name collision at a destination triggers an MD5
  compare: identical content → the inbox copy is dropped; different content → both are
  kept and you're told.
- **Credentials never get filed.** If recovery codes, API keys, or private-key files
  show up in the inbox, the skill refuses to file them into the drive and flags them
  for your password manager instead.
- **The health check is strictly read-only.** It reports; it never moves or deletes.

## Works on

Windows, macOS, and Linux. The skills use POSIX shell (`mv`, `md5sum`) with Windows
PowerShell equivalents noted inline — Claude picks whichever fits the machine it's on.

## License

MIT — see [LICENSE](LICENSE).
