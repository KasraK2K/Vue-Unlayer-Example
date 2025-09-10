# Vue Unlayer (vue-email-editor) Example

A minimal Vue 3 + Vite example that integrates the Unlayer email editor via `vue-email-editor`. It demonstrates:

- Loading the editor with a configured toolset and appearance
- Wiring Unlayer lifecycle hooks (`load`, `ready`)
- Handling image uploads and a custom user uploads provider
- Exporting the current design and HTML

This is a practical starting point to build an email template builder inside a Vue app.


## Features

- Vue 3 + Vite setup
- `vue-email-editor` (Unlayer) embedded editor
- Configurable tools, options, and appearance
- Image upload callback and custom `userUploads` provider
- Export HTML/Design button to capture output


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
  - Listens to `@load` and `@ready` events
  - Demonstrates Unlayer callbacks and providers:
    - `image` upload callback: reads the file and simulates an upload
    - `image:uploaded` event: adds images to in-memory gallery
    - `image:removed` callback: stubbed for server-side removal
    - `userUploads` provider: returns an in-memory list for the image gallery
  - `exportHtml()` triggers `editor.exportHtml()`; result is logged to the console and stored in component state

- `src/App.vue`
  - Renders the `Unlayer` component to full viewport height

- `index.html`, `src/main.js`, `vite.config.js`
  - Standard Vite + Vue bootstrapping and alias setup


## Usage Notes

- Project ID: Unlayer requires a valid Project ID. Set it via `VITE_PROJECT_ID` in `.env`. In this example it is read in `Unlayer.vue` and passed to `EmailEditor`.
- Image handling: The demo simulates upload by reading files locally with `FileReader`. Replace the `image` callback with real upload logic to your backend or storage. After successful upload, call the provided `done({ url, progress })` callback with the final URL.
- Custom image gallery: The `userUploads` provider returns images from in-memory state. Replace it with a request to your API and return items in the expected format.
- Persisting designs: `exportHtml()` stores the returned `design` and `html` in component state and logs them. Persist `design` server-side if you want to reload/edit later using `unlayer.loadDesign(...)`.
- Display mode: The editor is set to email display mode (`displayMode: 'email'`).


## Common Tasks

- Export the current email HTML
  - Click the "Export HTML" button when the editor is ready
  - Check the browser console to see the exported `design` and `html`

- Load a saved design
  - In `editorReady()`, call `unlayer.loadDesign(savedDesign)` once you fetch it from your backend

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

