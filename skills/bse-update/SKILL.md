---
name: bse-update
description: "Use when the user asks to check for or install Bricks Static Export skills updates, or when the plugin on the site seems newer than these skills describe. Compares the pack's verifiedUpTo with the plugin version on the site and, only on request, moves the checkout to the latest release."
allowed-tools: Bash, Read
---

# Bricks Static Export: check for skills updates

Prerequisite: the skills were installed from `https://github.com/builtforbricks/bricks-static-export-skills` (a git
checkout, by default at `~/.bricks-static-export/skills/bricks-static-export-skills`, with each skill folder symlinked into
the client's skills directory).

## What it does

Says whether this pack describes the plugin on the connected site and, when asked, updates the pack. The plugin and
the pack are released together, so a mismatch means one of them moved and the other did not.

## Steps

1. **The pack's version.** Read `verifiedUpTo` in `references/index.json` beside this file. Ignore `version` (the
   pack's own number) for this comparison.
2. **The site's version.** Call `bricks-static-export/preflight` and read `plugin_version`. Without the plugin's
   abilities (the owner has not switched them on), call `bricks/get-system-information` and read the `version` of the
   `plugins` entry named "Bricks Static Export", or ask the owner to run `wp bricks-static-export status` (the `plugin` row).
   With no site connection, say one is needed to compare, and offer the pack-side check alone (step 4).
3. **Compare as versions.** Major, minor, patch left to right; a pre-release suffix (`-dev`, `-beta.2`) sorts below
   the release.
   - Equal: current; say so.
   - Site newer: the pack is behind and may not know a new ability, command or setting; recommend the update and wait.
   - Site older: the pack is ahead; an ability or command it names may be missing on the site.
4. **The latest release**, changing nothing: `scripts/bse-skills-upgrade --check` from the repository root prints
   `BSE_SKILLS_INSTALLED`, `BSE_SKILLS_LATEST` and either `BSE_SKILLS_ALREADY_CURRENT` or
   `BSE_SKILLS_UPDATE_AVAILABLE <old> <new>`. Before the first release it prints `BSE_SKILLS_NO_RELEASE_FOUND` or
   `BSE_SKILLS_RELEASE_CHECK_FAILED`: say there is no release yet.

## Updating, only when the user asks

- `scripts/bse-skills-upgrade` moves the checkout to the latest release tag (prints `BSE_SKILLS_UPDATED <old> <new>
  <tag>`), stashing local changes first and saying so. `scripts/bse-skills-upgrade <tag>` moves to a named tag.
- If the checkout does not exist, clone it as the README says, then link the skills.
- The symlinks keep pointing at the same folders. **Start a new chat or session** afterwards: most clients load
  skills once, at the start.
