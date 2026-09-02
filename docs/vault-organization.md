# Personal & Work Vault Organization

A reference structure for organizing personal and work knowledge in Obsidian,
covering clinical, research, academic, and professional consulting activity.

This document describes the **method only** — no real content, contracts,
clinical data, or confidential material belongs in this (public) repository.
See the "GitHub structure" section below for where actual vault content
should live.

## Folder structure

```
ThiagoVault/
├── 00-Inbox/                        # quick capture, unsorted
├── 01-Personal/
│   ├── Finance/
│   ├── Health/
│   └── Journal/
├── 02-Work/
│   ├── Clinical/
│   │   ├── Hospital-A/
│   │   │   ├── Protocols-QA-QC/
│   │   │   ├── Equipment/
│   │   │   ├── Meeting-Notes/
│   │   │   └── Incident-Reports/
│   │   ├── Hospital-B/               # same substructure per hospital
│   │   └── Hospital-C/
│   ├── Research/
│   │   ├── Projects/
│   │   │   ├── Project-1/
│   │   │   └── Project-2/
│   │   ├── Topics/                   # e.g. molecular RT dosimetry, PET/SPECT
│   │   │                             # quantification, radiopharmaceutical
│   │   │                             # therapy, Monte Carlo, AI in imaging
│   │   ├── Literature/               # reading notes, links to Zotero library
│   │   └── Grants/
│   ├── Academic/
│   │   ├── University-of-Lucerne/
│   │   │   ├── Contracts-HR/
│   │   │   ├── Teaching/
│   │   │   └── Committees/
│   │   └── ZHAW/                     # same substructure
│   ├── Professional/
│   │   ├── Memberships/
│   │   │   ├── SGSMP/
│   │   │   ├── EANM/
│   │   │   ├── ESR/
│   │   │   ├── EFOMP/
│   │   │   └── BAG/                  # each: Membership-Docs, CPD-CME, Meeting-Notes
│   │   ├── CV-Bio/
│   │   └── Conferences-Talks/
│   └── Consulting/
│       ├── Boston-Scientific/
│       │   ├── Contracts-NDAs/
│       │   ├── Meeting-Notes/
│       │   ├── Deliverables/
│       │   └── Invoices-Payments/
│       └── Sego/                     # same substructure
├── 03-Templates/                     # meeting note, project note, literature templates
├── 04-Attachments/
└── 05-Archive/
```

## Cross-cutting organization

- **Tags** for things that cut across folders (`#hospital/A`, `#followup`,
  `#urgent`) instead of duplicating notes.
- **MOC (Map of Content) hub notes** — e.g. `Research Hub.md`,
  `Consulting Hub.md` — that link out to everything relevant in that area.
- **Dataview** plugin to build living dashboards (e.g. all open action
  items across hospitals or projects).
- Consistent naming: kebab-case or Title Case folders; meeting notes as
  `YYYY-MM-DD-topic.md`.

## GitHub structure

This repo (`thiagoluks/thiagoluks`) is the special GitHub profile repo —
only `README.md` on `main` is public-facing, but the whole repo is public.
It should hold this methodology document and nothing else personal.

Recommended split:

1. **`thiagoluks/thiagoluks`** (public, this repo) — profile README +
   generic organizational templates/methodology like this file. No real
   content ever.
2. **A separate *private* repo** (e.g. `thiagoluks/vault`) — the actual
   Obsidian vault, synced with the **Obsidian Git** community plugin
   (auto-commit/push on an interval or hotkey).

Inside the private vault repo:

- `.gitignore` excludes `.obsidian/workspace*`, `.trash/`, and any folder
  kept local-only.
- Treat `Clinical/`, `Consulting/*/Contracts-NDAs/`, and
  `Academic/*/Contracts-HR/` as **local-only** (gitignored) or encrypt
  with `git-crypt`/`age` before committing — clinical work, hospital
  policy, and consulting NDAs typically carry confidentiality obligations
  that a private repo alone does not satisfy. Never store patient-
  identifiable data in the vault at all.
- Everything else (research notes, literature, conference talks,
  membership admin) is generally lower-risk to version-control privately.
