---
name: setup-drive
description: Interview the user about their life domains and generate a personalized CLAUDE.md schema, folder skeleton, and seed filing map for an AI-managed drive. Use when the user says "set up my drive", "initialize life-admin-kit", "/setup-drive", or is starting from an empty or messy drive.
---

# Set Up the Drive

Turn the current folder (the user's drive root) into an AI-managed filing system:
interview the user, then generate a personalized `CLAUDE.md`, the folder skeleton,
and a seed `filing-map.md`. Templates live in this kit's `templates/` folder (or,
if installed as a plugin, alongside this skill at `../../templates/`).

Run this AT the drive root. If the current directory is clearly not a drive root
(it's a code repo, or a deep subfolder), ask before proceeding.

## Step 1 — Look before asking

Inventory what already exists: top-level folders, rough file counts, any existing
CLAUDE.md. Two important caveats:

- Cloud drives stream content — a folder that lists as empty may just not be
  hydrated yet. Never conclude "empty drive" from one listing.
- If a CLAUDE.md already exists, this is an *upgrade*, not a fresh setup: read it,
  preserve what works, and confirm changes with the user.

Use the existing folders as evidence when interviewing ("I see a folder called
`Taxes 2023` — should tax returns get their own category?").

## Step 2 — The interview

Ask in one or two compact batches, not twenty single questions. Cover:

1. **Identity of the drive**: personal, family, or business? What should it be
   called in the schema header? (Use a first name or handle — advise the user that
   CLAUDE.md need not contain their full legal name or ID numbers.)
2. **Core documents** (everyone has these): IDs, payslips or income records, tax
   returns — and **which country/countries** the tax paperwork belongs to, since
   that sets category names (e.g. "Renta/IRPF" in Spain, "Self-assessment" in the
   UK, "1040 + state" in the US) and typical recurring documents.
3. **Property**: do they own or rent out real estate? How many units? Mortgages?
   Tenants? → property domain with per-property subfolders (Contracts, Utilities,
   Insurance, Mortgage, Tenants, Others).
4. **Work**: employee, freelancer, business owner, or a mix? Multiple employers
   over time? → work/companies domain, plus freelance-invoice category if relevant.
5. **Investments**: brokers, funds, pension accounts, crypto? Annual declarations
   (many countries require overseas-asset reports)? → investments domain.
6. **Vehicles**: cars/motorbikes? → one folder per vehicle, plate number in the
   folder name (it's the natural key that appears on every document).
7. **Travel, learning, books, hobbies** — anything with recurring paperwork.
8. **Archives**: closed life chapters (old degree applications, a sold business)
   → `9X.` folders.
9. **Browser downloads**: does their browser save into the drive? If yes, that
   folder gets treated like the dropzone.
10. **Language(s)** their documents arrive in.

Skip questions the folder evidence already answers; confirm instead of asking.

## Step 3 — Generate the schema

From `templates/CLAUDE.md.template`, produce `CLAUDE.md` at the drive root:

- Include ONLY the domains the user actually has — an empty "Vehicles" category is
  noise, not future-proofing.
- Zero-padded numbering (`00. Dropzone` first, `99. Others` last, `9X.` archives).
- Fill every `{{PLACEHOLDER}}`; delete the template's instructional comments.
- Keep it under ~120 lines. It's a schema, not documentation.
- Do NOT put full legal names, national ID numbers, account numbers, or street
  addresses in CLAUDE.md — folder names and category descriptions are enough for
  filing, and the file may be shown in screenshots or shared setups.

## Step 4 — Create the skeleton

Create the top-level folders from the schema, plus:

- `00. Dropzone/README.md` — three lines: "Drop anything here. Say 'file my inbox'
  and Claude will name and file it. This folder should normally be empty."
- Second-level folders only where the schema names them (e.g. per-property
  subfolders); don't pre-create year folders — the filing skill creates them on
  demand.

If folders already exist with different names, propose renames as a plan and let
the user approve — renaming top-level cloud-drive folders can churn sync clients,
so batch it and warn about sync time on large drives.

## Step 5 — Seed the filing map

Copy `skills/file-inbox/references/filing-map.md` into the installed file-inbox
skill's `references/` folder and rewrite it for the generated schema:

- Fix every destination path to match the real folder names.
- Delete rows for domains the user doesn't have.
- Leave the example provider rows as comments so the skill knows the row format.
- Set the "Verified" date to today.

## Step 6 — Hand over

Report what was created, then tell the user the loop they now live in:

1. Save anything into `00. Dropzone/` — any filename, any time.
2. Say **"file my inbox"** whenever, or at the end of a session.
3. Once a month: **"run a drive health check"**.
4. The filing map learns their recurring documents automatically; they never edit
   it by hand (but they can).

Also pass on the two safety habits: deletions go to the drive's trash (Google
Drive keeps them 30 days), and credentials/recovery codes should live in a
password manager, never in the drive — the skills will flag them, not file them.

## Cross-platform notes

- Windows: use PowerShell `New-Item -ItemType Directory -Force`; always
  `-LiteralPath` for paths with accents/brackets; PowerShell variables are
  case-insensitive (`$r` clobbers `$R`).
- macOS/Linux: `mkdir -p` with quoted paths.
- The drive root path differs per client (Google Drive for desktop on Windows is
  often `G:\My Drive`; on macOS `~/Library/CloudStorage/GoogleDrive-<account>/My
  Drive`; OneDrive/Dropbox similar under the home folder). Confirm it with the
  user once and write it into the generated CLAUDE.md.
