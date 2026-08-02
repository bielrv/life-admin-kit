# Installing life-admin-kit

Two ways to install. Either way, you finish by running the setup interview.

## Option A — as a Claude Code plugin

The repo ships a `.claude-plugin/plugin.json` manifest, so it can be installed as a
plugin. From within Claude Code:

```
/plugin install life-admin-kit@<marketplace>
```

or add this repo directly (see the Claude Code docs for `/plugin marketplace add`
with a GitHub repo). The three skills (`setup-drive`, `file-inbox`,
`drive-health-check`) become available in every project.

Note: the `templates/` folder is used by the `setup-drive` skill; when installed as a
plugin the skill finds it relative to its own location.

## Option B — manual copy (no plugin system needed)

1. Copy the skill folders into `.claude/skills/` at your **drive root**:

   ```
   <drive root>/.claude/skills/setup-drive/SKILL.md
   <drive root>/.claude/skills/file-inbox/SKILL.md
   <drive root>/.claude/skills/file-inbox/references/filing-map.md
   <drive root>/.claude/skills/drive-health-check/SKILL.md
   ```

2. Keep `templates/CLAUDE.md.template` somewhere the setup skill can read it (easiest:
   copy the whole repo into `<drive root>/.claude/life-admin-kit/` and point the skill
   at it, or just keep the clone anywhere and mention its path when you run setup).

3. Open Claude Code at the drive root and run:

   ```
   /setup-drive
   ```

   The interview generates your personalized `CLAUDE.md` at the drive root and seeds
   the filing map. (You do not copy `CLAUDE.md.template` yourself — the skill fills it
   in for you.)

## After install

- Drop files into `00. Dropzone/`, then say `file my inbox`.
- Monthly: `run a drive health check`.

## Requirements

- Claude Code on Windows, macOS, or Linux.
- Your cloud drive mounted as a local folder (Google Drive for desktop, OneDrive,
  Dropbox, iCloud Drive…) — or any plain local directory.
- Optional but recommended: Python with `pypdf` (`pip install pypdf`) so the filing
  skill can read PDF contents to classify them.
