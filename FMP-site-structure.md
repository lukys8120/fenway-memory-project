# Fenway Memory Project: Site Structure

*Draft 11/12/2026. Covers structure and content only. Features like AR, player extras and synced transcripts are parked (see `FMP-ghost-site-brief.md` §5).*

---

## 1. Sitemap

```
fenwaymemoryproject.org
│
├── Home ─────────────── overview, mission, key facts, three doors in
│
├── Places ───────────── map of the Fenway (list view as v1 fallback)
│     └── Place profile     story + photos + audio, census data, current buildings
│
├── Issues ───────────── scrollable timeline of what happened to the neighborhood
│     └── Event story       year, story, photo gallery, clip, links to places/people
│
├── People ───────────── "book" of notable people (photo grid)
│     └── Person profile    portrait, bio, clip, links to places/events
│
├── Events ───────────── simple list of upcoming events, updated by hand
└── Support us ───────── donate, volunteer, share your story, newsletter
```

**Header:** the logo on the left and a **three-line (☰) menu button** on the right. The same button is used on phones and desktop. Tapping it opens the full menu, and it turns into an ✕ to close:
- Main links: Home · Places · Issues · People · Events · Support us
- Smaller links: About and team · Contact · Share your story · bitsandflow.org

**How it's built:** the menu links are edited in Ghost admin under Settings → Navigation, with no code. The open/close toggle is a small piece of theme code. Many official Ghost themes already include one.

**Footer:** About and team · Partner: bitsandflow · Contact · Social links

### Look and feel (draft)
Minimalist and editorial, modeled on the [La MaMa archive/about pages](https://lamama.org/la-mama-archive/), with green used only as an accent.

| Element | Choice |
|---|---|
| Page | White background, near-black text (`#151515`), muted gray for body copy (`#6E6E6A`) |
| Lines | Thin 1px rules (`#E3E3DF`) instead of boxes or cards. A solid black rule under the header and above the footer |
| Headings | Small, uppercase, letter-spaced section labels ("OUR MISSION", "IN THEIR WORDS") |
| Images | Large and dominant. Image on one side and text offset on the other (asymmetric, like La MaMa) |
| **Logo green** | **`#3EA547`**, sampled from the logo image the project lead shared (confirm against the original `.eps`). Used for the logo block, quote marks, the timeline line and dots, play buttons, link underlines, and map hover |
| Deep green | **`#2B7A32`** for small green text and the Donate button. The logo green is too light for small text on white (≈3:1 contrast); the deep green passes (≈5:1) |
| Pull quotes | Large green quote mark + uppercase quote + attribution, as on La MaMa |
| Font | A clean grotesk (mockup uses Archivo). Final pick still open |
| Menu | Three-line icon that animates into an ✕. Large uppercase links; the current page is shown in green |

---

## 2. How the content fits together in Ghost

Every profile is a **Ghost post**. Its **tags** say what kind of post it is and what it connects to.

| Type | Ghost object | Type tag | Connection tags |
|---|---|---|---|
| Place profile | Post | `place` | — |
| Event (issue) | Post | `issue` | `place-…`, `person-…`, theme (e.g. `housing`) |
| Person profile | Post | `person` | `place-…`, `issue-…`, category (e.g. `artists`) |
| Events list | One page, updated by hand | — | — |
| Home, Support, About | Page | — | — |
| Unlisted (e.g. future AR stops) | Post | add `#unlisted` | Hidden from lists, URL still works |

**Why tags matter:** if an event is tagged with a place and a person, all three pages can show "Connected" links to each other automatically. One upload shows up everywhere it's relevant.

### Where the oral-history clips go (decided)
Clips are embedded directly in the page they belong to:
- **Mostly People:** a clip goes on the profile of the person speaking or being discussed.
- **Places or Issues** when the clip is mainly about a location or an event.

A clip that fits more than one page can be added to each of them. Track them in a simple **clip inventory**: one row per clip, covering who, what it covers, which page it goes on, and consent. See the brief §4.

---

## 3. Page by page

### Home
- **Purpose:** explain the project in seconds and send people into one of the three sections.
- **Layout:** hero (photo/video, name, one-line mission) → mission and key facts → three big cards (Places / Issues / People) → featured story + next event → support banner.
- **Built with:** a custom homepage template. ✅ Fully doable.

### Places (map)
- **Purpose:** explore the Fenway geographically.
- **Ideal version:** a map of the Fenway where each block is shaded by data and can be clicked to open its profile.
- **Built with:** Ghost has no map. Use a free mapping library (**Leaflet**) inside a theme page, plus a **boundary file** of Fenway census areas (from the Census Bureau's TIGER/Line files). Each shape links to the matching place post.
- **v1 fallback:** a list/grid of places grouped by area, optionally with a static map image. The interactive map comes after.
- ⚠️ **Adjustments:**
  - **Pick the unit.** Census *blocks* are tiny, and the Fenway has a lot of them, so writing a story for every block isn't realistic. Most detailed census data (American Community Survey) is published at the **block group** level, not the block level. A workable split:
    - **Data** by block group or tract (shading on the map).
    - **Stories** for a curated set of places (streets, buildings, corners), shown as **pins**.
  - **Roads vs. blocks.** Streets cross block lines. Decide whether a "place" is an area, a street or a building. It can be a mix, but then the map needs shapes *and* pins.

### Place profile
- **Layout:** title → then/now photo → story text + audio clip → **data panel** (e.g. population, housing units, rent, jobs + small chart) → current buildings (name, use, year built) → connected people and events.
- **Built with:** a Ghost post (story, photos, audio are built-in blocks).
- ⚠️ **The data panel is the hard part.** Ghost has no data fields. Options:
  1. Type the numbers into a simple table by hand. Fine for a few places.
  2. Keep one data file (spreadsheet → JSON) and have the theme fill in each panel automatically.
  3. Embed charts from a free chart tool (e.g. Datawrapper).

  The data itself would come from the data-collection side of the project.

### Issues (timeline)
- **Purpose:** a scrollable timeline of what happened to the neighborhood. Each event opens its own story.
- **Layout:** intro → theme filters (Housing, Development, Community…) → vertical timeline of year + photo + title cards.
- **Built with:** a custom theme page that collects every `issue` post and lays them out in order.
- ⚠️ **Adjustment: storing dates.** Ghost sorts by publish date, and it's untested whether it accepts historical dates like 1965. The safe method is to **start each title with the year** ("1965 · Event title") and sort by title. Test backdating once the site exists.
- **Naming:** "Issues" could read as problems or magazine issues. Consider "Timeline" or "Neighborhood history" for the menu label.

### Event story
- **Layout:** year (large) → title → lead photo → story → photo gallery → oral-history clip → where it happened / who was involved → earlier/later navigation.
- **Built with:** a Ghost post with gallery and audio blocks. ✅ Fully doable.

### People ("book")
- **Layout:** intro → category filters (Activists, Artists, Business owners, Residents…) → grid of photo cards (name, years, role).
- **Built with:** a custom tag page showing all `person` posts. ✅ Easy.
- **Later:** a page-flip "book" effect is possible, but a grid comes first.
- ⚠️ **Living people:** need consent for photos and bios.

### Person profile
- **Layout:** portrait → name, years, role → bio → **interview clips** (the main home for oral-history clips) → connected places and events.
- **Built with:** a Ghost post. ✅ Fully doable.

### Events (decided)
- **Lives on:** the FMP Ghost site.
- **Layout:** a simple list (date, event name, venue, short description or link).
- **Built with:** one Ghost page that a volunteer updates by hand when events are added or have passed. ✅ Built in, no theme work needed.

### Support us
- **Layout:** why support matters → Donate button → volunteer / share your story / newsletter cards → nonprofit (EIN) line.
- **Built with:** a Ghost page with a button linking to the donation link (Stripe or bitsandflow). ✅ Built in.

### Search: parked
Not a priority right now. Ghost has a built-in search pop-up that can be switched on later.

---

## 4. Feasibility at a glance

| Page | Ghost does it | Theme work | Outside tool / data | Difficulty |
|---|---|---|---|---|
| Home | | ✓ | | Easy |
| Places map | | ✓ | Leaflet + census boundaries | **Hard** (v1: list) |
| Place profile | ✓ | | Census data panel | Medium |
| Issues timeline | | ✓ | | Medium |
| Event story | ✓ | | | Easy |
| People book | | ✓ | | Easy |
| Person profile | ✓ | | | Easy |
| Events | ✓ | | | Easy |
| Support | ✓ | | Donation link | Easy |
| ☰ Menu toggle | ✓ (links) | ✓ (toggle) | | Easy |

---

## 5. Decisions

**Decided**
- **Clips** go inside the page they belong to: mostly People, otherwise Places or Issues.
- **Events** live on the FMP Ghost site as a simple list page, updated by hand.
- **Menu** uses a three-line (☰) button.
- **Search** is parked.
- **Look:** minimalist, La MaMa-style, with logo green `#3EA547` as an accent only (see Look and feel).

**Still open**
1. **Place unit:** census block, block group, tract, street or building? Or data areas + story pins?
2. **Map in v1** or list first?
3. **Data panel:** which numbers, and who supplies them?
4. **Timeline dates:** year-in-title method, or test backdating?
5. **Menu labels:** "Issues" vs. "Timeline" / "Neighborhood history".
6. **People:** who counts as "notable", and what are the categories?
7. **Is "About" separate** from Home, or is the homepage's mission section enough (plus a footer link)?
