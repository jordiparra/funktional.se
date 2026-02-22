# Surprise Travel Site — Reuse Prompt

Use this prompt to recreate this site for a different destination. Paste it into a new Claude conversation along with the details for your trip.

---

## Prompt

I want to build a surprise travel reveal site — a single `index.html` file that I can share with someone before we leave on a trip. They open it and don't know where we're going yet.

**How it works:**
1. Dark hero screen. Big question: "Where are we going?" and a "Find out" button.
2. Button click fades out the text and starts a slot-machine animation — random European city names cycle through at increasing intervals, then slow and lock on the real destination with a bounce animation.
3. Confetti fires when it locks in.
4. Below the destination name, "We're going to" fades in, and a "Things to do" scroll hint appears at the bottom.
5. Scrolling reveals a white details section with:
   - The neighborhood/area where we're staying, with a description and photo
   - A list of places/things to do, each with a name, description, and photo
   - A photo credits section at the bottom

**Design:**
- Font: Inter (Google Fonts)
- Dark hero: `#111` background, white text
- Details section: white background, `#111` text, `#666` for descriptions
- Place images: 3:2 aspect ratio, `border-radius: 2px`, lazy loaded
- Slot display: large (`clamp(3.5rem, 20vw, 8rem)`), gray until locked, then white
- Confetti from bottom of screen (canvas-confetti CDN)
- `noindex, nofollow` meta tag — this is private, not for search engines
- `no-scroll` on body until after reveal, then scroll is unlocked
- Details section is `visibility: hidden` until first scroll after reveal

**Fill in the following for the new trip:**

### Destination
- **City name** (what locks in on the slot): `[e.g. Barcelona]`
- **Slot machine city pool**: A list of ~25 plausible European city names to cycle through (the real city should NOT be in this list, but similar-sounding or same-region cities can be)

### Where we're staying
- **Neighborhood/area name**: `[e.g. Gràcia]`
- **Description**: A short paragraph about the area — what it's like, what makes it distinctive, what's nearby. Tone: informative, warm, not overly tourist-brochure. Use `<em>` for foreign-language words.
- **Photo filename**: `assets/[neighborhood-slug].jpg`

### Things to do / Places
For each place, provide:
- **Name**: `[e.g. Sagrada Família]`
- **Description**: 2–4 sentences. Practical tips, what to expect, anything worth knowing. Same tone as above.
- **Photo filename**: `assets/[place-slug].jpg`
- **Photo credit**: Attribution line for Wikimedia Commons (license + author)

Aim for 10–16 places. Mix of landmarks, museums, neighborhoods, food spots. If there's a personal/inside-joke item (like the Warhammer store was for this Paris trip), include it last.

### Photos
Source all images from Wikimedia Commons. For each image:
1. Search Wikimedia Commons for the place
2. Pick a photo with a permissive license (CC BY, CC BY-SA, or public domain)
3. Download a reasonably sized version (aim for ~800–1200px wide)
4. Save to `assets/[slug].jpg`
5. Note the credit: `[Place] © [Author] [License]`

---

## Technical notes for Claude

- The slot machine uses a `schedule` array of millisecond delays — 30 fast ticks at 55ms, then progressively slower: `70, 90, 120, 160, 220, 300, 420, 580, 800, 1100, 1500`. On the last tick it shows the real city, then `lockCity()` fires.
- In `runSlot()`, the second-to-last tick shows `'Amsterdam'` as a red herring (this was a decoy for the Paris trip — update this to a plausible alternative for the new destination).
- The `lockCity()` function (rename from `lockParis()`) sets the destination text and adds the `locked` CSS class, then sequences: confetti at +400ms, scroll hint at +900ms.
- The details section uses `visibility: hidden` (not `display: none`) so it doesn't affect layout, and becomes visible on first scroll.
- All images go in `assets/` alongside `index.html`.
- The file is self-contained — no build step, no dependencies beyond CDN fonts and confetti.

---

## Folder structure

```
surprise/
  index.html
  assets/
    [neighborhood-slug].jpg
    [place-1-slug].jpg
    [place-2-slug].jpg
    ...
  prompt.md   ← this file (do not upload)
```

To deploy: drop the folder into the GitHub Pages repo alongside other site folders. It will be accessible at `funktional.se/surprise/` (or equivalent). The `noindex` meta tag keeps it out of search results.
