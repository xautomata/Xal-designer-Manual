# CLAUDE.md — XAL Designer Manual Project

This file provides context for Claude (VS Code extension or CLI) when working on this project.
Read this before making any changes to the documentation.

---

## What this project is

This is the **documentation manual for the XAL Designer** — the visual editor used to design,
configure, and publish automata in the XAUTOMATA platform.

The manual is built with **MkDocs + Material theme**. It is a **bilingual project**: English is
the default language, Italian is the secondary language, managed via the MkDocs Material `i18n`
plugin.

### Target audience
- **Operators and administrators** who configure automata via the XAL Designer
- **Delivery team and developers** who work with the XAL language reference

### Tone and purpose
This is an **operational manual**, not a technical reference.
- Focus on **what to do and how to do it**
- Avoid internal technical details unless strictly necessary
- Write in the second person ("clicca", "seleziona", "apri" in Italian; "click", "select", "open" in English)
- Short sentences, direct language

---

## i18n setup

The project uses the MkDocs Material `i18n` plugin with the following convention:

- `page.md` → **English** (default, served at `/page/`)
- `page.it.md` → **Italian** (served at `/it/page/`)

Images are **shared** between languages — paths are identical in both versions.
The language switcher is rendered automatically by the Material theme.

### Bilingual maintenance rules

The Italian translation is **not yet complete** — `.it.md` files need to be created for each page.
A future session will perform the full translation.

**Once translation is complete, any change to the English version must be reflected in the Italian version.** This applies to:
- New pages: create both `page.md` and `page.it.md` together
- Content edits: apply the equivalent change to the `.it.md` file in the same session
- Structural changes (sections added/removed): mirror them in Italian

Do not leave the two versions out of sync. If a factual correction is made to the English source, fix the Italian too.

---

## Italian adaptation rules

The XAutomata UI is in **English**. Italian pages must handle UI terms intelligently:

### General principle
Translate the prose into natural Italian, but **preserve English UI labels** exactly as they
appear in the interface (button names, menu items, field labels, section titles).

### Strategies by case

| Situation | Strategy | Example |
|---|---|---|
| UI element used inline | Keep English, add Italian gloss on first use | `il pannello **Overview Graph** (grafico d'insieme)` |
| UI element repeated | Use English alone after first introduction | `Apri l'Overview Graph` |
| Ambiguous term | English in parentheses | `il pannello di filtro (*filter panel*)` |
| Purely descriptive concept | Translate fully | `clicca sul pulsante di salvataggio` |

### Do not
- Translate button labels, field names, or menu items that appear verbatim in the UI
- Invent Italian equivalents for product-specific terms (e.g. "Automaton", "Dispatcher", "XAL", "Probe")
- Use overly formal or bureaucratic Italian — keep the same direct, operational tone as the English version

### Gender of English loanwords
- **dashboard** is feminine: "la dashboard", "le dashboard", "una dashboard"
  - Adjectives and participles must agree: "condivisa", "visualizzate", "stessa", "mostrata", ecc.

### Second person
- Use the **informal "tu"** form throughout: "clicca", "seleziona", "apri", "inserisci"
- Avoid "Lei" or passive constructions

---

## Writing style rules

These rules are established and must be followed consistently:

### Language
- **English pages**: always English, second person imperative ("Click **Save**")
- **Italian pages**: Italian prose + English UI labels, informal "tu", present tense
- Present tense throughout in both languages

### MkDocs Material syntax
- Admonitions: `!!! info`, `!!! warning`, `!!! note`, `!!! example`
- Image captions: `/// caption` / `///` block immediately after the image
- Always number captions: `Fig.1 - Description`, `Fig.2 - Description`

### Images
- Path convention: `../images/<section>/<subsection>/<filename>.png`
- Always include a caption with figure number

### Internal links
- Always use relative paths

### Page structure
- Each page starts with a short intro sentence (1-2 lines max) explaining what the page covers
- Use `---` horizontal rules to separate major sections
- Tables for structured comparisons (buttons, fields, roles)
- Numbered lists for sequential steps
- Bullet lists for non-sequential items

---

## Project structure

```
Xal-designer-manual/
├── mkdocs.yml                  # MkDocs navigation and config
├── CLAUDE.md                   # This file
├── requirements.txt            # Python dependencies for CI
├── version.txt                 # Version file for CI tagging
└── docs/
    ├── index.md                # Manual home page
    ├── images/                 # All screenshots and images
    ├── concepts/               # Conceptual introduction to automata
    │   ├── automaton.md
    │   ├── states_transitions.md
    │   ├── clocks.md
    │   ├── actions_metrics.md
    │   ├── families.md
    │   └── platform_integration.md
    ├── designer/               # Operational guide to the XAL Designer UI
    │   ├── overview.md
    │   ├── getting_started/
    │   │   ├── opening_file.md
    │   │   └── interface.md
    │   ├── overview_graph/
    │   │   ├── reading.md
    │   │   ├── correlation_types.md
    │   │   └── opening_automaton.md
    │   ├── automata/
    │   │   ├── managing.md
    │   │   ├── global_state.md
    │   │   └── clocks.md
    │   ├── states/
    │   │   ├── creating.md
    │   │   ├── actions.md
    │   │   └── metrics.md
    │   ├── transitions/
    │   │   ├── creating.md
    │   │   ├── types.md
    │   │   ├── metric_clocks.md
    │   │   └── transition_new.md
    │   └── publishing/
    │       └── commit.md
    └── reference/              # XAL language technical reference
        ├── file_structure.md
        ├── automaton.md
        ├── states.md
        ├── transitions.md
        ├── actions.md
        ├── metrics.md
        ├── global_state_clocks.md
        ├── parameters.md
        ├── queries.md
        └── examples/
            ├── ecommerce.md
            ├── patcher.md
            └── c2_intercept.md
```

---

## Translation status

**Italian translation: not started.**

All pages currently exist only in English (`.md`). When the translation session begins:
1. For each `page.md`, create the corresponding `page.it.md` alongside it
2. Translate prose to Italian following the adaptation rules above
3. Keep English UI labels verbatim
4. Reuse the same image paths (images are shared)

---

## Screenshot workflow

Screenshots are provided by the user during the documentation session.
When a page references screenshots that are not yet available, use placeholder paths
following the naming convention and note them for the user to fill in.

Placeholder format:
```markdown
![Description](../images/section/subsection/filename.png)
/// caption
Fig.N - Description (screenshot pending)
///
```
