# Foundry Private Markets Group website

Production-ready static website for Foundry Private Markets Group at Southern Methodist University. The site is a single, build-free HTML file deployed with Cloudflare Workers Static Assets.

## Repository structure

```text
.
├── .assetsignore
├── index.html
├── wrangler.jsonc
└── fpmg-assets/
    └── team/
        ├── carson-kudelko.jpg
        ├── clayton-ellis.jpg
        ├── james-joseph.jpg
        ├── jb-watson.jpg
        ├── quoc-an-nguyen.jpg
        └── vinicius-domingues.jpg
```

## Local preview

Do not test the event registration form from a `file://` URL. Serve the repository over HTTP:

```bash
python3 -m http.server 8765 --bind 127.0.0.1
```

Then open `http://127.0.0.1:8765/`.

## Membership application configuration

The production configuration is centralized in `APPLICATION_CONFIG` inside `index.html`:

```javascript
var APPLICATION_CONFIG=Object.freeze({
  formUrl:'https://forms.gle/Wan1e75dWhoez9t6A',
  deadline:'',
  recruitmentStatus:'open',
  timezone:'America/Chicago',
  clubEmail:'smuprivatemarketsgroup@gmail.com'
});
```

- `formUrl` accepts only HTTPS URLs on `forms.gle` or `docs.google.com/forms`.
- `deadline` accepts `YYYY-MM-DD` and closes at 11:59:59 PM in `America/Chicago`.
- `recruitmentStatus:'closed'` overrides the deadline and disables both Apply buttons.
- An empty or invalid deadline does not close applications.
- An empty or invalid form URL displays the Coming Soon state and disables both Apply buttons.

The current deadline is intentionally blank, so recruitment stays open until an official date is supplied or `recruitmentStatus` is changed to `closed`.

## Event registration

Event registration posts directly to FormSubmit at `smuprivatemarketsgroup@gmail.com`. The form requires an `@smu.edu` address, opens FormSubmit in a new tab, and refuses submission from `file://` pages. The first production submission may require the club inbox to activate FormSubmit.

## Cloudflare Workers deployment

The current production Worker and GitHub repository are both named `fpmg-webiste`. The spelling must remain consistent until a coordinated rename is approved.

Cloudflare Workers Builds is configured with:

- Production branch: `main`
- Build command: none
- Deploy command: `npx wrangler deploy`
- Root directory: `/`
- Static asset directory: repository root (`.`)

`wrangler.jsonc` is the source-controlled Workers Static Assets configuration. `.assetsignore` prevents repository metadata, Wrangler state, documentation, and temporary files from being published as web assets.

Every push to `main` triggers a production deployment. Verify the apex custom domain, the Worker URL, `/#/join`, and all team images after each deployment.

Hash routing (`#/team`, `#/events`, and `#/join`) works without server-side rewrite rules because URL fragments are handled in the browser.

## Release integrity

Production snapshot prepared on 2026-10-11:

```text
index.html  8d513de3fc3beea83929d051693f4443b74988f9d7102766beaa9e008e417655
```

## Release checklist

Before enabling the public production URL:

- Set or explicitly approve the blank application deadline.
- Confirm the Google Form is published and accepting responses.
- Confirm the Google Form requires one PDF resume of at most 10 MB, contains no Resume Link question, uses the approved confirmation message, and writes responses/uploads to the intended Google Sheet and Drive.
- Confirm the club inbox has activated FormSubmit and complete one authorized staging registration test.
- Confirm whether the public Instagram handle should remain `foundry_smu` or change to `fpmg_smu`.
- Confirm permission to publish all six member photos and remove EXIF metadata if required by club policy.
- Add production-domain metadata (`canonical`, `og:url`, and a public `og:image`) after the final domain is known.

Do not submit the Google Form or FormSubmit registration as part of routine deployment verification without separate authorization.
