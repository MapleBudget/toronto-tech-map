<!--
Thanks for contributing to BUILD416! Fill out the relevant section below.
All PRs are reviewed by the maintainer before merging.
-->

## What Kind Of Change Is This?

- [ ] Adding a new company
- [ ] Correcting an existing company (coordinates, funding, domain, etc.)
- [ ] Updating the ticker
- [ ] Code / design change
- [ ] Other (describe below)

---

## If Adding Or Editing A Company

**Company Name:**

**Office Address (In The Mapped Area):**

**Website / Domain:**

**Funding Status:** <!-- e.g. Seed, Series B $70M, Public (TICKER), Bootstrapped, Acquired -->

**Source For Funding Info:** <!-- link to announcement, press release, Crunchbase, etc. -->

### Checklist

- [ ] Coordinates fall inside the Toronto core bbox `[-79.46, 43.62]` → `[-79.34, 43.68]`
- [ ] `domain` is a bare domain (no `https://`, no `www`) that resolves to a real favicon
- [ ] This company isn't already in `companies.json` (no duplicate)
- [ ] JSON is valid: `python3 -m json.tool companies.json > /dev/null` passes
- [ ] I ran it locally and confirmed the pin lands in the right place

---

## If A Code / Design Change

**What Does It Do?**

**How Did You Test It?**

- [ ] Ran locally over HTTP and confirmed the map loads and the loader dismisses
- [ ] Markers stay pixel-locked when panning/zooming
- [ ] No new dependencies; still a single self-contained `index.html`
- [ ] Solarium green (`#056540`) remains the only accent color

---

## Anything Else?

<!-- Context, screenshots, questions for the maintainer -->
