# Local visual editor

The production page loads `editor-overlay.js` only when opened from `file://`,
`http://localhost`, or `http://127.0.0.1`. The editor is excluded from the
Cloudflare asset upload by `.assetsignore`, so public visitors never receive the
editing controls.

For the most reliable preview, serve the repository and open the page over HTTP:

```bash
python3 -m http.server 8765 --bind 127.0.0.1
```

Then open `http://127.0.0.1:8765/` and click **Chỉnh sửa**. Draft text and image
changes are stored only in that browser. They do not update `index.html` or the
deployed site until they are applied to source and deployed.
