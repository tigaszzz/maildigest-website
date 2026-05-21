# maildigest.app — static landing site

Static marketing site for Mail Digest. Hostable on Cloudflare Pages, Netlify, GitHub Pages, or any static host.

## Files

```
index.html       Home page
privacy.html     Privacy policy
terms.html       Terms & conditions
styles.css       Shared styles (editorial light theme, design system from DESIGN.md)
logo.svg         Brand mark (used in <img>)
favicon.svg      Browser tab icon
_headers         Security headers (Cloudflare Pages format)
README.md        This file
```

No build step. No JavaScript. Drop the folder into Cloudflare Pages.

## Deploy on Cloudflare Pages

1. Push this folder to a GitHub repo (e.g. `maildigest-website`).
2. Cloudflare dashboard → **Workers & Pages → Create application → Pages → Connect to Git**.
3. Pick the repo.
4. **Build settings:**
   - Framework preset: *None*
   - Build command: *(leave empty)*
   - Build output directory: `/`
5. Deploy.
6. Add the custom domain `maildigest.app` under the Pages project → Custom domains. Cloudflare will manage DNS + TLS automatically if the domain is on the same account.

Cloudflare Pages will pick up the `_headers` file and apply the security headers.

## Local preview

```bash
# Any static server works. Pick one:
python3 -m http.server 8080
# or
npx serve .
```

Then open http://localhost:8080.

## Design system

This site follows the design system documented in the parent project's `DESIGN.md`:

- Typography: DM Serif Display (headlines), Syne (UI/body), DM Mono (metadata/badges)
- Colours: warm off-white background `#f7f5f2`, primary text `#1a1917`, priority palette inherited from the desktop app
- Borders: always 0.5px, no shadows, no gradients
- Behaviour: instant transitions (max 0.12s), hidden scrollbars

Do not edit fonts, colours, or borders without updating the parent project's `DESIGN.md` to keep desktop app + site visually aligned.

## Copy and content

All copy is in English. If you need to translate, copy `index.html`, `privacy.html`, `terms.html` into a subfolder (e.g. `pt/`) and update the `lang="en"` attribute and links.

## Legal pages

`privacy.html` and `terms.html` are written for an open-source MIT-licensed project. They are not a substitute for legal advice. If the project becomes commercial (e.g. you sell a hosted variant), have a lawyer review and replace these.
