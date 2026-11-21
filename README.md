# Foundry Private Markets Group website

Production-ready static website for Foundry Private Markets Group at Southern Methodist University. The site is a single, build-free HTML file deployed with Cloudflare Workers Static Assets.

## Repository structure

```text
.
├── .assetsignore
├── README.md
├── index.html
├── wrangler.jsonc
├── local-editor/
│   ├── README.md
│   └── editor-overlay.js
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

### Direct visual editing

When the site is opened from `file://`, `localhost`, or `127.0.0.1`, a local
**Chỉnh sửa** toolbar appears at the bottom of the page. It supports inline text
editing, team-photo replacement, hero-background replacement, and logo
replacement. **Lưu bản nháp** stores changes only in that browser; ask Codex to
apply the saved draft to source and deploy it when the edits are ready.

The production HTML contains no editor toolbar or `contenteditable` markup. A
strict protocol/hostname guard loads the overlay only on `file://`, `localhost`,
or `127.0.0.1`; `.assetsignore` also excludes `local-editor/` from Cloudflare
uploads. Public visitors therefore receive no editing controls.

## Membership application configuration

The production configuration is centralized in `APPLICATION_CONFIG` inside `index.html`:

```javascript
var APPLICATION_CONFIG=Object.freeze({
  formUrl:'https://forms.gle/eTCvtC9Q15wakFLW7',
  deadline:'',
  recruitmentStatus:'closed',
  timezone:'America/Chicago',
  clubEmail:'smuprivatemarketsgroup@gmail.com'
});
```

- `formUrl` accepts only HTTPS URLs on `forms.gle` or `docs.google.com/forms`.
- `deadline` accepts `YYYY-MM-DD` and closes at 11:59:59 PM in `America/Chicago`.
- `recruitmentStatus:'closed'` overrides the deadline and disables both Apply buttons.
- An empty or invalid deadline does not close applications.
- An empty or invalid form URL displays the Coming Soon state and disables both Apply buttons.

Recruitment is currently closed. The Apply status reads `Close` with a red dot and both Apply buttons are disabled. The deadline remains blank; reopening requires an explicit configuration update.

## November 2026 content update

The Team page includes ten Analysts below the six existing Executive Board profiles. Profile text and photos come from the verified `foundry-10-members-update.zip`; Shrey Shori retains the supplied temporary headshot.

The James Thomason guest speaker event took place on November 16, 2026, at Cox School of Business – DMBQ 1315. It is archived with `data-status="end"`, displays `end`, and offers no registration or calendar action. Its date is stored without a time because no event time was provided. Previously concluded events also display `end`.

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

### Production release procedure

1. Serve the repository locally and verify `#/`, `#/team`, `#/events`, and `#/join`.
2. Confirm saved local-editor content has been applied to `index.html`.
3. Run `git diff --check`, review `git diff`, and commit `.assetsignore`,
   `README.md`, `index.html`, and `local-editor/` together.
4. Push `main`; Cloudflare Workers Builds deploys the commit automatically.
5. Verify `https://foundryprivatemarketsgroup.org/` and the Worker URL.
6. Confirm `/local-editor/editor-overlay.js` returns `404`, the live DOM has no
   `#fpmg-editor-toolbar` or `[contenteditable]`, and the Apply status, red dot, and disabled Apply buttons reflect the closed
   recruitment configuration.

## Release integrity

Production snapshot prepared on 2026-10-13:

```text
index.html  2da99f10d7e0cbf9651283274ba88585acc42babf8e626489cfbf3bf754b3775
```

## Release checklist

Before enabling the public production URL:

- Set or explicitly approve the blank application deadline.
- Confirm the Google Form is published and accepting responses.
- Confirm the Google Form requires one PDF resume of at most 10 MB, contains no Resume Link question, uses the approved confirmation message, and writes responses/uploads to the intended Google Sheet and Drive.
- Confirm the club inbox has activated FormSubmit and complete one authorized staging registration test.
- Confirm whether the public Instagram handle should remain `foundry_smu` or change to `fpmg_smu`.
- Confirm permission to publish all six member photos and remove EXIF metadata if required by club policy.
- Add a dedicated public social-sharing image and configure `og:image` when an approved asset is available.

Do not submit the Google Form or FormSubmit registration as part of routine deployment verification without separate authorization.
