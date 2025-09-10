# Vue Unlayer (vue-email-editor) Example

A minimal Vue 3 + Vite example that integrates the Unlayer email editor via `vue-email-editor`. It demonstrates:

- Loading the editor with a configured toolset and appearance
- Wiring Unlayer lifecycle hooks (`load`, `ready`)
- Handling image uploads and a custom user uploads provider
- Exporting design/HTML from a parent component via an exposed method

This is a practical starting point to build an email template builder inside a Vue app.


## Features

- Vue 3 + Vite setup
- `vue-email-editor` (Unlayer) embedded editor
- Configurable tools, options, and appearance
- Image upload callback and custom `userUploads` provider
- Parent-level export via `ref` and exposed `exportHtml()`


## Quick Start

Prerequisites:
- Node.js: `^20.19.0 || >=22.12.0` (see `package.json` engines)

Install dependencies:

```bash
npm install
```

Environment setup:

1) Duplicate `.env.example` to `.env`.
2) Set your Unlayer project ID in `.env`:

```bash
# .env
VITE_PROJECT_ID=YOUR_UNLAYER_PROJECT_ID
```

- You can create a free Unlayer account and project to obtain a Project ID.
- For local experimentation you may use any valid Unlayer project ID you manage.

Run the dev server:

```bash
npm run dev
```

Open the app at the URL printed by Vite (typically `http://localhost:5173`).

Build for production:

```bash
npm run build
npm run preview    # optional: preview the production build
```


## Where Things Live

- `src/components/Unlayer.vue`
  - Hosts the `<EmailEditor />` component from `vue-email-editor`
  - Passes configuration: `tools`, `options`, `appearance`, `projectId`, `locale`, `minHeight`
  - Accepts a required `user` prop and forwards it to Unlayer via `options.user.id`
  - Listens to `@load` and `@ready` events
  - Demonstrates Unlayer callbacks and providers:
    - `image` upload callback: reads the file and simulates an upload
    - `image:uploaded` event: adds images to in-memory gallery
    - `image:removed` callback: stubbed for server-side removal
    - `userUploads` provider: returns an in-memory list for the image gallery
  - Exposes `exportHtml()` via `defineExpose({ exportHtml })`; it calls `editor.exportHtml()` and returns the latest exported data `{ design, html, amp, chunks }`
  - Optionally preloads a saved design during `@load` using `unlayer.loadDesign(...)` if you provide one

- `src/App.vue`
  - Renders a top-level "Export" button
  - Holds a `ref` to the `Unlayer` component and calls the exposed `exportHtml()` method: `unlayerRef.value.exportHtml()`
  - Passes a sample `:user="1111"` to `Unlayer`

- `index.html`, `src/main.js`, `vite.config.js`
  - Standard Vite + Vue bootstrapping and alias setup


## Usage Notes

- Project ID: Unlayer requires a valid Project ID. Set it via `VITE_PROJECT_ID` in `.env`. In this example it is read in `Unlayer.vue` and passed to `EmailEditor`.
- Image handling: The demo simulates upload by reading files locally with `FileReader`. Replace the `image` callback with real upload logic to your backend or storage. After successful upload, call the provided `done({ url, progress })` callback with the final URL.
- Custom image gallery: The `userUploads` provider returns images from in-memory state. Replace it with a request to your API and return items in the expected format.
- Persisting designs: `Unlayer` exposes `exportHtml()` so the parent can retrieve `{ design, html, amp, chunks }`. Persist the returned `design` server-side to reload later via `unlayer.loadDesign(...)` (e.g., in the `@load` handler).
- Display mode: The editor is set to email display mode (`displayMode: 'email'`).
- User context: Provide your app's user identifier via the required `user` prop on `Unlayer`. It is passed to Unlayer as `options.user.id`.


## Common Tasks

- Export the current email HTML/design
  - Click the top "Export" button in the app
  - The parent calls `unlayerRef.value.exportHtml()`; check the console for `{ design, html, amp, chunks }`

- Load a saved design
  - After fetching from your backend, call `unlayer.loadDesign(savedDesign)` (the example shows doing this in the `@load` hook if data exists)

- Add/remove tools
  - Edit the `tools` object in `Unlayer.vue` to enable or configure additional blocks


## Tech Stack

- Vue 3
- Vite 7
- `vue-email-editor` (Unlayer)


## References

- Unlayer Docs: https://docs.unlayer.com/
- vue-email-editor: https://github.com/unlayer/vue-email-editor
- Unlayer Callbacks/Events: https://docs.unlayer.com/docs/callbacks


## License

This is an example project. Use it freely in your own applications.
