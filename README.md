# Fenway Memory Project: Website Planning

Planning material for the **Fenway Memory Project (FMP)** website: an oral-history site about the Fenway neighborhood in Boston, to be built on [Ghost](https://ghost.org). FMP is a project of [bitsandflow](https://bitsandflow.org), with technical work by Northeastern University PHIL3000 service-learning students (Fall 2026).

**Current focus:** site structure and content. Features like AR, synced transcripts and site search are parked for later.

## What's here

| File | What it is |
|---|---|
| [`FMP-wireframe.html`](FMP-wireframe.html) | Clickable wireframe of the whole site. Open it in any browser |
| [`FMP-site-structure.md`](FMP-site-structure.md) | Sitemap, page-by-page layouts, look and feel, what Ghost can and can't do, decisions made and still open |
| [`FMP-ghost-site-brief.md`](FMP-ghost-site-brief.md) | Background: what the project lead wants, Ghost explained in plain English, hosting and media limits, open questions, clip inventory template |
| [`ghost-nonprofit-platform-spec.md`](ghost-nonprofit-platform-spec.md) | Reference spec shared by the project lead (10/30/2026). It mostly describes the bitsandflow events/journal site, not FMP |

## Viewing the wireframe

Download or clone the repo and double-click `FMP-wireframe.html`. No server or install is needed.

- The **three-line menu** (top right) moves between sections.
- Click map blocks, timeline events and people cards to open their profile pages.
- The **wireframe note** under each page explains how it would be built in Ghost.

To share it as a live link, turn on GitHub Pages for this repo (Settings → Pages) and point it at `FMP-wireframe.html`.

## Site at a glance

```
Home ─ mission, overview, three doors in
├── Places   map of the Fenway → place profile (story, census data, buildings today)
├── Issues   scrollable timeline → event story (photos, clip)
├── People   "book" of notable people → person profile (main home for interview clips)
├── Events   simple list, updated by hand
└── Support  donate, volunteer, share your story
```

Look: minimalist and editorial (modeled on the La MaMa archive site), white page, logo green `#3EA547` used only as an accent.

## Status

- [x] Gather the project lead's reference material and goals
- [x] Draft sitemap, page layouts and wireframe
- [ ] Settle the open decisions (see `FMP-site-structure.md` §5)
- [ ] Build the media and clip inventory
- [ ] Pick a Ghost plan (Publisher or higher needed for a custom theme) and set up the account
- [ ] Build the theme (starting from Ghost's official Starter theme)

## Note on privacy

This repo is **public** (agreed with the project lead). Keep it that way safely:

- **No personal names** of team members or contacts. Interviewees only once they've agreed to be published.
- **No contact details:** no emails, phone numbers or home addresses.
- **No unreleased media:** never commit audio, photos or transcripts without cleared consent. Raw interview files stay in the shared drive (the `.gitignore` already blocks common audio and video formats).
- **No keys or passwords:** Ghost API keys, Stripe keys and similar go in a local `.env` file, which git ignores.
