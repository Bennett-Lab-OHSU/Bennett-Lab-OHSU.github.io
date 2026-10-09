# Lab website with a live ink-wash landscape

A static Jekyll site for GitHub Pages. A procedurally generated Chinese-style
landscape ({Shan, Shui}* by Lingdong Huang) sits behind the pages and slides
sideways as you scroll. Content lives in plain files, in the style of the
[Fraser Lab site](https://github.com/fraser-lab/fraser-lab.github.io).

## Put it online

1. Create a GitHub repo named `<owner>.github.io`, where `<owner>` is the exact name of the account
   or organization that owns it, and push these files.
2. In the repo, go to Settings, then Pages, and publish from the main branch.
3. Settings, Pages shows your real address. If it ends with the repository name (for example
   `https://owner.github.io/some-repo/`), set `baseurl: "/some-repo"` in `_config.yml`. If it is just
   `https://owner.github.io`, leave `baseurl` empty.

## Preview on your computer

```
bundle install
bundle exec jekyll serve      # http://localhost:4000
```

## Change things

| To do this | Edit this |
|---|---|
| Lab name, two letters for the browser-tab icon, the tagline under the name, contact details | `_config.yml` |
| A different landscape | `landscape.seed` in `_config.yml` (any word). Try one live with `?seed=word` on the URL |
| Trees and mountains only, or with pagodas, houses, boats and people | `landscape.structures` in `_config.yml` (`false` is trees only) |
| How fast it slides, idle drift, how far apart pages start | `landscape:` block in `_config.yml` |
| The About text on the home page | `index.md` |
| Menu items | `_data/navigation.yml` |
| Group names on the People page | `_data/people.yml` |
| Email addresses | Write them as `name [at] domain` in `_config.yml` and member files. Never type a real `@` |
| The two buttons on the home page | `_layouts/home.html` |
| Add a person | copy `_members/rivera.md` to a new file and edit it |
| Move someone to alumni | set `status: alumni` and add `enddate` (and optionally `subsequent`) |
| Add a paper | copy a file in `_publications/` |
| Add a preprint | same, with the extra line `type: preprint`. When it is published, delete that line and update `journal` and `pub_date` |
| Add news | new file in `_posts/` named `YYYY-MM-DD-short-title.md` |
| Colors and fonts | the tokens at the top of `assets/css/site.css` |
| Research, Join, Contact text | `research/index.md`, `join/index.md`, `contact/index.md` |

A person's `category` is matched to `_data/people.yml` ignoring capital letters and stray spaces.
Anyone whose category matches no group appears under "Other" on the People page, so nobody goes missing.

Headshots: upload a photo to `static/headshots/` named after the person's file in `_members`
(for `_members/jane-smith.md`, upload `jane-smith.jpg`; `.jpeg`, `.png` and `.webp` also work).
It appears automatically to the right of that person's stamp and description on the People page,
and below their name on phones. Crop to 4 wide by 5 tall, about 600 x 750 pixels, under 300 KB.
To use a photo with a different name, add `image: /static/headshots/that-name.jpg` to the person's file.
Each person also has a red stamp with their initials; it appears on the People page only, not in the page header.
The stamp's edge, thickness, colors and (switched-off) motion are described in section 4.5 of the manual.

## How the landscape works

- `assets/js/shanshui/shanshui-core.js` is the upstream generator with its UI removed.
  `_dev/extract-core.mjs` rebuilds it from upstream.
- `assets/js/shanshui/landscape-worker.js` generates scenery off the main thread and
  returns SVG tiles. The same seed always gives the same painting.
- `assets/js/landscape.js` draws each tile once onto a canvas and pans them with one
  CSS transform, so scrolling stays cheap.
- `assets/js/site.js` swaps pages without reloading, so the painting never restarts.
  It glides to where the next page begins. Links always fall back to normal loads.
  Don't put inline `<script>` tags in pages: swapped-in content doesn't run them.
- Reduced-motion visitors get a still painting. The landscape needs JavaScript; without
  it the site is plain paper and text.

## Known limits

- First visit spends a few seconds generating scenery in the background, mostly on
  low-end phones. It skips the background pre-drawing on low-memory or data-saver devices.
- Fonts are included in `assets/fonts/`, so there are no requests to Google or anyone else.
- Not included from the Fraser template: tag pages, an RSS feed, course pages.

## Security

This is a static site: no database, no logins, no forms, no cookies, nothing stored in
visitors' browsers. What is in place:

- **Content Security Policy** (in `_layouts/default.html`): only this site's own files can
  load or run. Third-party scripts, fonts, images and connections are blocked, and so are
  inline scripts and inline event handlers. If you add something that loads from another
  site (an analytics script, an embedded video), you must add that address to the policy on
  purpose.
- **No outside resources.** Fonts and the generator are copied into the repo.
  `PROVENANCE.md` records exactly where each came from, with a commit and file hashes.
- **Email addresses are never printed in page source.** They appear as `name [at] domain`
  and become a mail link only in the visitor's browser. This stops most address-harvesting
  robots, not a determined person.
- **Everything typed into data files is escaped** before it is shown, and outside links
  (member websites, paper links) are only drawn if they start with `https://` or `http://`.
  A `javascript:` link in a member file simply doesn't appear.
- **Outside links** open with `rel="noopener noreferrer"`.
- **Dependabot** (`.github/dependabot.yml`) opens a pull request when the Ruby build tools
  need a security update.

What a static site on GitHub Pages cannot do: send security headers such as
`X-Frame-Options` or `Strict-Transport-Security`, so a CSP written as a page tag cannot
stop other sites from putting yours in a frame (GitHub Pages already serves HTTPS; turn on
"Enforce HTTPS" in Settings, Pages). Raw HTML that someone writes inside a Markdown page or
news post is trusted, which is why only people you trust should be able to commit.

**Settings to turn on in your GitHub organization** (these are not files in this repo):
two-factor authentication required for all members; branch protection on `main` requiring
a reviewed pull request; Dependabot alerts and secret scanning; keep the number of
people with write access small.

## Licenses

Three parts, explained in `LICENSE`: the website code is MIT; the lab's own text, photos
and publication lists are "all rights reserved" unless you decide otherwise; and the
third-party pieces ({Shan, Shui}* generator, the Fraser Lab site structure, the fonts) keep
their own licenses, whose notices must stay. Before publishing, open `LICENSE` and replace
the two `[FILL IN]` lines with your lab or institution.
