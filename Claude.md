# Repo: personal Zettelkasten (Obsidian vault)

This repo is the user's Obsidian vault ("My Vault/") — a Zettelkasten of things they've read or learned, kept for later recall, not a coding project.

- `My Vault/Notes/` — one markdown file per concept/topic. This is where new material goes.
- `My Vault/Books/` and `My Vault/papers/` — PDF storage only. Don't generate notes there.
- `My Vault/Articles/`, `My Vault/Tutorials/` — currently unused; leave alone unless asked.

## When given a link (or links) to save

1. Search `My Vault/Notes/` for an existing note on the same or a closely related topic (by filename and by grepping content). If one exists, extend it — don't create a duplicate.
2. Fetch the link and actually read enough to understand the material, not just the title.
3. Create (or extend) `My Vault/Notes/<Topic>.md`, filename matching the note's `# Title`, following the vault template (`My Vault/Templates/Template 1.md`):
   ```
   # {{Title}}

   DATE:  {{dd-mm-yy, today}}

   Tags: [[Related existing note]]

   # References:
   {{link(s) given, plus any others actually used}}

   # Content:
   {{note body — see formatting rules below}}
   ```
   - `Tags` should point to whatever related note(s) already exist in the vault — check before inventing a new one.
   - If extending an existing note: append the new material under its own `###` subheading and add the new link to `# References:`; don't silently overwrite what's there. If the new source contradicts something already written, say so explicitly instead of quietly replacing it.
4. Tell the user which file was created or extended, and a one-line summary of what's now in it.

## Content formatting (inside "# Content:")

Same ADHD-friendly principles as the global chat rules, adapted for a note that gets skimmed cold, months later, instead of read in a live conversation:

- **Open with 1-2 lines saying what the thing is and why it matters**, before any history or buildup. A future skim of just that line should be enough to decide whether to read on.
- **Headers over paragraphs.** The moment a topic has more than one facet, break it into named `###` subsections — see `My Vault/Notes/MCP.md` for the pattern (Architecture / Servers / Clients as separate sections).
- **Bullets over prose.** One idea per bullet, **bold** the term it defines. Cap a list at ~5 items; past that, split into subgroups with their own header.
- **Concrete over abstract.** Real code, real numbers, a named example — the actual config, not "this can be configured."
- **Tables for comparisons** (tools, options, features) instead of describing differences in prose.
- **No filler.** Cut throat-clearing, hedging, and restating the source verbatim — compress to what's actually worth recalling later.
