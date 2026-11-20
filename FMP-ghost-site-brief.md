# Fenway Memory Project: Ghost Website Brief

*Prepared November 2026 from the project lead's notes and reference material.*

---

## 1. What the project lead wants

### The big picture
- **Two sites.** The first is **bitsandflow.com**, the parent nonprofit arts org. The second is **fenwaymemoryproject.org**, the oral-history site. FMP has its own URL but should also appear as a section of bitsandflow. There's a logo for each.
- **The team.** A small group of students handles the technical build in Ghost. Other cohort members curate content.
- **The first ask.** Map out what the site should look like (a sitemap and a wireframe), then go through the media and draft a **content schema**.
- **Admin expected to be easy afterwards.** Setting up the ghost.org account, pointing DNS, and linking Vimeo, SoundCloud and a donation account.

### FMP in the project lead's own words
> "A landing page for a neighborhood oral history which can be used by an arm chair or walking tour audience. Selected segments of audio and/or [video] interviews with historic postcards, newspaper clippings and photographs will be part of the AR experience by location OR accessible by QR code or menu… study categories by topic, issue, time period or location (block or building in street). Maybe there is a Pokémon Go element of badges earned/collected."

### Distilled requirements
| | What | Phase |
|---|---|---|
| Raw material | About **35 hours of interviews**, cut into **~3-minute excerpts** | v1 |
| Story page | Excerpt (audio/video), plus historic postcards, clippings, photos, context | v1 |
| Ways in | Browse by **topic/issue**, **time period**, **location (block/building)**; menu; **QR codes** on signs | v1 |
| Two audiences | **Armchair** (at home) and **walking tour** (on the street) | v1 |
| Map | Locations with links to stories | v1 (probably) |
| AR | Location-triggered AR. *"Second phase… just keep in mind for compatibility"* (10/30) | v2 |
| Gamification | Badges for visiting places or collecting stories | v2/v3 ("maybe") |
| Media hosting | Video on **Vimeo** (domain-restricted), audio on **SoundCloud** | v1 |
| Money | Donations (and maybe merch), lean option preferred (Stripe) | v1 on bitsandflow? |
| Look | Workshop poster for **color/hue reference**; [La MaMa archive](https://lamama.org/la-mama-archive/) as a reference site (simple but text-heavy) | v1 |

### Where the AI reference material agrees
- **Each story is a Ghost post**, and **tags** create the browse paths. One story can show up under Place, Era and Theme at once.
- **Ghost is the storytelling layer only.** Heavy media lives on Vimeo or SoundCloud, and any future AR lives on an AR platform that gets embedded.
- **QR codes point to permanent story URLs**, so signs never need reprinting.
- **Launch without AR**, but build the content model so AR can be added later without restructuring.

### Where it conflicts or is still open
- **Payments.** The `.md` spec and PDF say Stripe only. The Odoo email describes Odoo ERP plus n8n automations. These are very different amounts of work.
- **Theme approach.** The threads offer three starting points: customize an official theme (Dawn, Casper or Source), start from Ghost's **Starter theme** (a bare developer template on GitHub), or build from scratch. The AI tool each thread happened to name (Kimi, Claude, Codex) doesn't matter. Any of them, including Claude Code, can work from any starting point. **Leaning:** Starter theme + Claude Code. It's a clean, Ghost-compatible base with build, zip and validation commands already set up, and FMP's layout isn't a standard blog anyway.
- **Catalog.** The ChatGPT thread asks whether **Omeka S**, a dedicated archive/catalog tool, should sit next to Ghost.
- **Which doc is which site.** The `.md` spec (Events / Journal / Installations / tickets) reads like **bitsandflow**. The PDF blueprint (stories / podcasts / clips) reads like **FMP**.
- **Placeholder content.** The PDF's taxonomy is generic ("North End, Downtown, East Ward", "Immigration, Labor…"). None of it is Fenway-specific yet.
- **Ghost plan.** The spec says "Starter/Creator plan". **Starter can't use a custom theme** (see §2), so that won't work.
- **User submissions.** Options range from a simple "Share Your Story" contact form to a moderated upload pipeline (Tally → n8n → Odoo).

---

## 2. Ghost in plain English

**What it is.** Ghost is an open-source publishing platform (a CMS), like a leaner, more modern WordPress built around articles, newsletters and memberships. Volunteers write in a block editor, and a **theme** controls how everything looks.

### Ghost's building blocks
| Ghost term | What it is | What it would be for FMP |
|---|---|---|
| **Post** | A dated content item | **One story or excerpt** |
| **Page** | A static item | About, Support, Share Your Story |
| **Tag** | The main organizing tool. Public tags show on the site; tags starting with `#` are hidden and used by the theme's logic | Place, Era, Theme, Voice; `#oral-history`, `#walking-tour` |
| **Card** | A block inside the editor: audio, video, gallery, image, embed, button, HTML | Vimeo/SoundCloud embed, photo gallery of postcards and clippings |
| **Theme** | Handlebars (`.hbs`) templates plus CSS. `default.hbs` is the wrapper, `index.hbs` the lists, `post.hbs` a single story, `tag.hbs` the browse pages | The look and layout of the site |
| **routes.yaml** | A file that sets the URL structure and "collections", e.g. `/places/` lists everything tagged as a place | Clean browse URLs |
| **Code injection** | Site-wide scripts in the header/footer | Map embed, later geolocation |
| **Members / newsletter** | Built-in email signup, plus paid tiers through Stripe | "Get updates" signup |

### What Ghost is *not*
- **Not a map tool.** A map has to be embedded or added to the theme.
- **Not an AR platform.** AR gets embedded.
- **Not a media library.** Large video and audio should live on Vimeo or SoundCloud.
- **Not a structured database.** The archive needs structured data per story: interview date, speaker, coordinates, building. Historically that meant cramming it into tags or the post body. Ghost's pricing page now lists **"custom fields"** as a paid-plan feature. Check what it covers, because it could solve this.

### Hosting and cost (from ghost.org/pricing, checked 11/11/2026, yearly billing)
| Plan | Price | Custom theme? | Staff users | Notes |
|---|---|---|---|---|
| Starter | $18/mo | **No** (default theme only) | 1 | No API, integrations or custom fields |
| **Publisher** | $29/mo | **Yes** | 3 | API, webhooks, integrations. 1,000 members |
| Business | $199/mo | Yes | 15 | Needed for a subdirectory install (+$50/mo) |
| Self-hosted | Free software plus ~$6–12/mo server | Yes | Unlimited | You maintain updates, backups and security |

**Bottom line:** if FMP needs a custom layout (and it does), plan on **Publisher** at minimum. Two separate sites would mean **two subscriptions**.

> **Name clash:** the **Starter *theme*** (a free developer template) has nothing to do with the **Starter *plan*** ($18/mo hosting). Anything built from the Starter theme counts as a custom theme, so it needs Publisher or above, or self-hosting.

### What lives in Ghost vs. what gets embedded
Ghost(Pro) caps the size of **each uploaded file** by plan: **Starter 5 MB, Publisher 100 MB, Business 250 MB**. There's no cap on the number of files.

| Content | Typical size | Where it lives | Why |
|---|---|---|---|
| Story text, captions, credits | Tiny | **Ghost** | This is what Ghost is for |
| Transcripts | Tiny | **Ghost** (post body) | Readable on the page; good for accessibility |
| Photos, postcards, clipping scans (web versions, ~2000px) | 0.3–3 MB | **Ghost** (image/gallery cards) | Ghost auto-resizes images for phones |
| **3-min audio excerpt (MP3)** | ~3–7 MB | **Ghost** (audio card) on Publisher, *or* SoundCloud | Fits easily under 100 MB. A native player means no third-party ads or "up next" from strangers |
| PDFs (full newspaper page, documents) | 1–20 MB | **Ghost** (file card) | Fits on Publisher |
| **3-min video excerpt** | ~70–180 MB at 720p–1080p | **Vimeo, embedded** | Often over the 100 MB cap. Ghost also plays the raw file with no adaptive streaming, so it would stutter on a phone signal on the street. Vimeo adjusts quality, adds captions, and can lock playback to your domain |
| Raw interview masters (35 hrs of WAV/video) | Many GB | **Neither:** archival storage (drive or cloud, backed up) | Only the published excerpts go online |
| Map, AR, 3D models (later) | — | **Embedded** from a map tool / AR platform / Sketchfab | Ghost has no map or AR engine |

**Upshot:** video is the only excerpt format that *has* to be embedded. Audio can go either way. On the Starter plan's 5 MB cap, even audio gets tight (a higher-quality 3-minute MP3 is ~4–7 MB), which is one more reason to go with Publisher.

---

## 3. Open questions for the project lead

### Structure and ownership
1. **One Ghost site or two?** "Own URL but also a section on bitsandflow" could mean either:
   - One Ghost site with FMP as a tagged section, plus the FMP domain pointed at it.
   - Two Ghost sites that link to each other.

   This choice drives cost, theme work and admin.
2. Does a Ghost account exist yet, and on which plan? Who owns the **domain registrar** logins for both domains (needed for DNS)?
3. What does **v1 launch** mean, and when? Options include a 30-day prototype, a 3-story-hub pilot, or 20–30 stories.
4. Who is the **long-term content owner** after the semester? This decides how custom the theme can be.

### Content (the core of the content schema)
5. How many excerpts are **already cut**, and how many go into v1? Where are the files now?
6. Are the excerpts audio only, or video too? Do the **Vimeo and SoundCloud accounts** exist yet?
7. Do **transcripts** exist? Should the full transcript appear on the page?
8. **Consent and releases.** Can we publish names, faces and full audio? Does anything need redacting?
9. What are the **real Fenway categories**: topics/issues, time periods, locations (blocks/buildings)? Who decides the taxonomy?
10. Postcards, clippings and photos: are they **digitized**? Are the **rights cleared**? Who holds them?
11. Is there a list of **tour locations** with addresses or coordinates? This may come out of the Fenway walk.

### Features
12. **Homepage priority:** armchair visitors or people walking the neighborhood?
13. Is a **map** in v1? If so, what kind: a simple pinned map, or something interactive? (The project's data work may feed into this later.)
14. Is **"Share Your Story"** in v1? Is it a simple form or a moderated upload?
15. Do **donations and merch** live on FMP, on bitsandflow, or both? Stripe or Odoo? Is the org a 501(c)(3) or fiscally sponsored? (Needed for Stripe setup.)
16. Is a **newsletter signup** wanted?
17. Are badges and gamification phase 2/3?

### Design
18. Besides the poster hues, are there **fonts** or a style guide? Any other reference sites, liked or disliked?
19. **Build approach:** customize an official theme (Source/Dawn) or build a custom theme?

### Information still missing
- A real inventory of the media.
- The workshop poster at full size (I only saw the thumbnail).
- The "Signage & Vimeo Playback" email (10/30), which I didn't open.

---

## 4. Strawman for the content session

### Clip inventory (one row per clip)
Clips are embedded in the page they belong to: mostly the People profile, otherwise a Place or Issue page (see [`FMP-site-structure.md`](FMP-site-structure.md)). A shared spreadsheet keeps track of them:

| Column | Example |
|---|---|
| Clip ID | FMP-012 |
| Title | "Growing up on the block" |
| Speaker / interviewee | Name or anonymized label |
| Interview date, interviewer | 2025-06-12 |
| Length | 3:05 |
| File location | Drive folder / Vimeo link |
| What it covers | Places, events and people mentioned |
| Goes on page(s) | Person: … / Place: … / Issue: … |
| Archival items to show with it | Postcards, clippings, photos plus captions and credits |
| Consent status | Full / partial / anonymized / not yet cleared |
| Transcript | Only where one exists and is relevant |
| Status on site | Draft / Unlisted / Public |

### "Unlisted" pages (e.g. AR-tour stops before the tour exists)
Ghost has no built-in "unlisted" switch, but two setups give the same result:

| Option | How it works | Good for | Watch out |
|---|---|---|---|
| **Draft + preview link** | Leave the post as a draft and share its preview link (`/p/…`). Opening it needs no login | Showing the project lead or the team a page in progress | Not a real public URL, so don't print it on a QR sign |
| **Published + hidden tag** | Publish the post with an internal tag such as `#ar-tour`. The theme leaves anything with that tag out of the homepage, browse pages and menus. Its URL still works | Tour stops that need a permanent URL now (e.g. a test QR sign) before the tour launches | Still in the sitemap, so search engines can find it. Ghost's built-in search may list it. Add a "noindex" tag to the post to stay out of search engines. Test that the direct URL still loads |

When the AR tour is ready, removing the tag (or adding a public "Walking tour" tag) makes those stops appear everywhere. The URLs don't change, so printed QR codes keep working.

### FMP sitemap
**Moved to [`FMP-site-structure.md`](FMP-site-structure.md).** The structure is now Home · Places (map) · Issues (timeline) · People (book) · Events · Support, behind a three-line menu button, and that file has page-by-page layouts, the feasibility table and open decisions.

### Suggested agenda for a 3-hour working session
1. **15 min.** One site or two. What v1 means.
2. **45 min.** Media inventory, then lock the clip inventory columns above.
3. **45 min.** Sitemap, plus sketches of the homepage and Story page.
4. **Rest.** Ghost plan and account, who does what, next deadline.

---

## 5. Parked for later (not the current focus)
The current goal is **site structure and content**. These come after:
- **Transcript features:** timestamped transcripts that highlight as the clip plays and let you click to jump, plus chapter marks on the player. Not every clip has a transcript, and not every transcript is relevant.
- **Player extras:** a walking-tour playlist, clips that start playing when opened from a QR sign, a "then and now" photo slider, and a player that keeps playing across pages.
- **AR experiences, GPS triggers, badges/gamification.** Phase 2+. For now, tour stops can exist as unlisted pages (see §4).
- **Payments beyond a simple donate link** (Odoo, merch, ticketing).
- **Site search.** Ghost's built-in search can be switched on later.
