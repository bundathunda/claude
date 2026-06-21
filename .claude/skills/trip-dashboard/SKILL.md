---
name: trip-dashboard
description: Build a single-file static trip/getaway dashboard website (rooms, flights, carpools, money split, schedule, photo gallery) deployable to GitHub Pages. Use when the user wants a trip dashboard, group getaway site, festival trip page, or similar — a self-contained index.html with no build step.
---

# Trip Dashboard Skill

Generates a single self-contained `index.html` trip dashboard: dark hero, sticky sidebar nav (mobile: horizontal scroll nav), photo gallery with lightbox, and sections for guests, rooms, flights, carpools/vehicles, money split, schedule, lineup (if a festival/event trip), good-to-know, and what-to-bring. No frameworks, no build step, deployable straight to GitHub Pages.

`template.html` in this skill directory is a working reference implementation (from the ABGT700 trip) — copy it as the starting point rather than writing from scratch.

## Workflow

1. **Gather trip details from the user.** Ask only for what's missing; don't ask about sections that don't apply (e.g. skip "lineup" for a non-festival trip). Useful info to collect:
   - Trip name, dates (check-in/out), location, lodging link (Airbnb/VRBO/hotel)
   - Guest list and room assignments
   - Flights (arrivals/departures: who, date, time, flight number, route)
   - Ground transport: rental car(s) or carpool vehicles with trip legs and passengers
   - Money: total cost, per-person split, who to pay and how
   - Schedule/itinerary by day
   - Festival/event lineup if applicable (day, set times, stage)
   - Good-to-know notes and what-to-bring list
   - Photos: if the user has an Airbnb/VRBO listing HTML file, extract photo URLs via regex (see below); otherwise leave `photos: []` and skip the gallery section

2. **Copy `template.html` to the target repo as `index.html`.** Edit the `const TRIP = {...}` object at the top of the `<script>` block — that's the only place trip-specific data lives. Section rendering, CSS, and the lightbox are generic and shouldn't need changes unless a section is being added/removed entirely.

3. **Extracting photos from an Airbnb/VRBO HTML export:**
   ```bash
   grep -oP 'https://a0\.muscache\.com/im/pictures/[a-f0-9\-]+\.jpg(?=")' listing.html | sort -u
   ```
   Pick ~15 unique photo URLs, put them in `TRIP.photos`. The gallery renders the first as a large hero image plus 4 thumbnails, with the rest reachable via the lightbox. Images are requested with `?im_w=` query params for responsive sizing — keep that pattern.

4. **If a section doesn't apply** (e.g. no festival lineup, no flights for a road-trip-only group), remove its `NAV` entry, its `<section>` block, and its render block in the script — don't leave an empty/dead section.

5. **Sanity-check before pushing:**
   - Days of the week match the actual calendar dates (a recurring source of bugs in past trips — verify with `date -d "Sept 11 2025" +%A` style checks, don't eyeball it)
   - Money math: total / guest count = per-person, and the displayed numbers agree
   - Every named guest in flights/carpools/rooms actually appears in the `guests` array
   - Photos load (spot check a couple URLs with `curl -I`)

6. **Deploy.** This skill only produces the file — follow the target repo's own deployment process (see its README, typically: push `index.html` to `main`, enable GitHub Pages "Deploy from branch" in repo settings). If working from a sandboxed/scoped session that can't push directly to the live repo, the file can be fetched via raw GitHub URL and pushed manually — check the project's README for that pattern before assuming it's needed.

## Data shape reference (TRIP object)

```javascript
const TRIP = {
  name, checkin, checkout, nights, airbnb,        // strings
  photos: [ "https://...jpg", ... ],               // optional, omit section if empty
  guests: [ "Name", ... ],
  rooms: [ { id, bed, max, occupants: [...] }, ... ],
  vehicles: [                                       // preferred over old rentalCar+carpools split
    { name, trips: [ { direction, date, time, passengers: [...] }, ... ] },
    ...
  ],
  lineup: [ { day, event, start, color, acts: [...] }, ... ],  // festival trips only
  flights: {
    arrivals:   [ { who: [...], date, time, flight, note, early }, ... ],
    departures: [ { who: [...], date, time, flight, note, late }, ... ],
  },
  money: {
    totalRental, perPerson, guestCount, payTo, payWhen,
    notes: [ { icon, label, detail }, ... ],
  },
  schedule: [ { day, items: [ { time, desc, note }, ... ] }, ... ],
  goodToKnow: [ ... ],
  whatToBring: [ ... ],
};
```

Note: `vehicles` superseded an earlier `rentalCar` + `carpools` split — use `vehicles` for new trips, it's more flexible (handles N vehicles each with multiple legs/passenger sets).
