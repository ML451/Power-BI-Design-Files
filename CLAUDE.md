# CLAUDE.md — Power BI Design Files

A gallery of Power BI dashboards, custom SVG visuals, themes and practice case studies,
kept as a reference and learning library. Fork of a public design gallery, with local
additions.

**Most of this repository is binary and not editable here.** Of ~450 files: 120 `.pbix`,
58 `.png`, 33 `.pdf`, 22 `.mp4`, 15 `.xlsx`. A `.pbix` is a zip archive — it cannot be
meaningfully read, diffed, reviewed or edited from a session, and attempting it wastes a
turn and produces nothing. Treat every `.pbix`, image, video and workbook as an opaque
asset: move it, rename it, describe it, reference it — never open it expecting content.

**The editable surface is narrow and is where the actual work happens:**

| Surface | Files | What it is |
|---|---|---|
| Markdown | ~121 | READMEs and the two authored SVG documents below |
| SVG | 41 | reusable visual sources |
| Theme JSON | 9 | `Theme .JSON Files/` — community themes, sourced not authored |
| CSV | 17 | practice datasets |

## The two documents that carry real authored content

Everything else in this repo is a showcase asset. These two are the reusable knowledge, and
they are the ones worth being careful with:

- **`SVG Visuals/SVG_BEST_PRACTICES.md`** — the consolidated rule set for writing SVGs inside
  Power BI DAX measures. Every rule is labelled either a **default** (apply unless there's a
  stated reason not to) or a **caution** (situational — flag it, never silently apply or
  silently ignore). Preserve that distinction when editing; collapsing cautions into defaults
  is how a nuanced rule set turns into a checklist that gives wrong answers.
- **`SVG Visuals/SVG_PROMPT_TEMPLATE.md`** — a paste-ahead prompt block derived from the
  best-practices document. **It is downstream of that document by design**: if
  `SVG_BEST_PRACTICES.md` changes, update the template in the same change, or the two start
  giving contradictory instructions to whoever pastes the template.

### The SVG encoding rule, because it breaks everything else

Percent-encoding is decoded in **one pass over the whole string** before the SVG is parsed,
so a single bad escape breaks the entire data URI — not just the attribute it appears in.
The failure looks like "the visual is blank", which points nowhere near the cause.

- Every `#` becomes `%23` (`fill='#0000FF'` → `fill='%230000FF'`).
- Every `%` becomes `%25` — including `width="100%25"` and in label text (`"72%25"`).
- After **any** edit, re-scan the full string and confirm every `%` is followed by exactly
  two hex digits. Checking once at the start is not enough; a later edit reintroduces it.

Also load-bearing: exactly one root `<svg>`; a `viewBox` matching the real coordinate space;
`display="block"` on the root (inline SVG otherwise reserves a baseline gap that misaligns
the visual). A deliberately fixed pixel size is a legitimate choice, not a mistake — but say
so explicitly, and if a measure relies on the Table/Matrix "Image size" Format-pane property
instead of `width`/`height`/`viewBox`, comment the exact size it assumes. Nothing else keeps
that Format-pane setting and the DAX in sync; they are two places that desync silently.

## Layout

```
Full Dashboards/      complete dashboard examples with datasets and background images
Native Visuals/       techniques using only Power BI's built-in visuals
SVG Visuals/          custom SVG visuals + the two authored documents above
Practice/             Datacamp case studies, Datasets/, Starter PBIX Files/
Theme .JSON Files/    community theme JSONs (BIBB, Zebra BI, Kerry Kolosko)
```

## Sharp edges

- **Almost every path contains spaces**, and one folder is literally named
  `Theme .JSON Files` — a space before the dot. Quote every path. Unquoted globs over this
  repo fail in ways that look like missing files.
- **Themes are sourced, not authored.** `Theme .JSON Files/README.md` credits each theme to
  its contributor. Don't edit a community theme in place and leave it under the original
  attribution — copy it under a new name if you need a variant.
- **This is a fork of a public gallery.** Check upstream before diverging on shared content,
  and keep the attribution in the root README intact. Local additions (the SVG documents,
  the Practice case studies) are the parts that are genuinely ours.
- **`.mp4` and `.vtt` files travel in pairs** — video plus its caption track. Moving one
  without the other silently orphans the captions.

## When adding to the gallery

Put the file in the folder that matches how it was built, not what it depicts: native-visual
technique → `Native Visuals/`, SVG measure → `SVG Visuals/`, complete report →
`Full Dashboards/`. Update the containing folder's README in the same change — a gallery
whose index lags its contents stops being navigable, and there is no tooling here that will
catch the drift.
