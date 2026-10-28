# HelloGitty for Chrome

A Chrome extension that shows your GitHub pull requests and issues, sorted by whose move it is, with the count waiting on you in the toolbar badge.

It renders the same "Your work" board as the [HelloGitty](https://github.com/FawwazLawal/hellogitty) dashboard — Your move, Waiting on others, Finished — with the same one-sentence statuses. The classifier runs as WebAssembly compiled from HelloGitty's own `internal/work` package, so the two can never drift apart.

## Views

- **Popup** and **side panel**: compact layout with search and "Needs me" filter.
- **Full tab**: checks for a running HelloGitty dashboard at `localhost:7340` and opens it if found; otherwise renders the work board in the dashboard's own design.

## Prerequisites

- **Go 1.22+** (for the WebAssembly build)
- A local checkout of [hellogitty](https://github.com/FawwazLawal/hellogitty) — the build compiles `wasm/main.go` inside it (the classifier imports its internal packages) and extracts the dashboard's CSS and JS from `web/index.html`

## Build

```bash
# If hellogitty-clone is a sibling directory (the default):
./build.sh

# Otherwise, point to your checkout:
HELLOGITTY_ROOT=/path/to/hellogitty-clone ./build.sh

# Bake in the OAuth app's public client ID for device-flow sign-in:
HELLOGITTY_CLIENT_ID=Ov23li... ./build.sh
```

The build produces `dist/hellogitty/` (the unpacked extension) and `dist/hellogitty-extension-<version>.zip` (for the Chrome Web Store). Builds are deterministic: fixed timestamps and a sorted file list make the zip byte-identical across runs.

## Load unpacked

1. Go to `chrome://extensions`
2. Turn on **Developer mode**
3. Click **Load unpacked** and pick the `dist/hellogitty` folder

## Files

| File | Purpose |
|---|---|
| `manifest.json` | Chrome MV3 manifest |
| `background.js` | Service worker: alarms, sign-in, fetch, classify, badge |
| `board.html` | Popup, side panel and full view |
| `board.js` | Renders the dashboard's markup from the stored board |
| `extension.css` | Rules the dashboard stylesheet doesn't cover |
| `config.js` | OAuth client ID and scope (empty by default) |
| `wasm/main.go` | WebAssembly entry point (compiles `internal/work`) |
| `icons/` | Extension icons |
| `build.sh` | Builds `dist/` from this repo and a hellogitty checkout |

## License

See the [HelloGitty license](https://github.com/FawwazLawal/hellogitty/blob/main/LICENSE).
