<template>
  <div id="app">
    <button v-if="editorStatus === 'ready'" @click="exportHtml">Export HTML</button>

    <div class="container">
      <EmailEditor
        :appearance="appearance"
        :min-height="minHeight"
        :project-id="projectId"
        :locale="locale"
        :tools="tools"
        :options="options"
        ref="emailEditor"
        @load="editorLoaded"
        @ready="editorReady"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
import { EmailEditor } from 'vue-email-editor'

const emailEditor = ref(null)
const editorStatus = ref('')
const state = reactive({ design: {}, html: {}, images: [] })

const minHeight = '500px'
const locale = 'en'
const projectId = Number(import.meta.env.VITE_PROJECT_ID)

// prettier-ignore
const tools = {
  columns: { enabled: true },
  heading: { enabled: true },
  text: { enabled: true, properties: { text: { value: '<p>Your email content here…</p>' }, fontFamily: { value: 'Arial, Helvetica, sans-serif' }, fontSize: { value: '14px' }, lineHeight: { value: '1.5' } } },
  image: { enabled: true, properties: { altText: { value: '' }, display: { value: 'block' }, imageUrl: { value: '' } } },
  button: { enabled: true, properties: { text: { value: 'Call to Action' }, borderRadius: { value: '4px' }, fullWidth: { value: false } } },
  divider: { enabled: true },
  social: { enabled: true },
  menu: { enabled: true },
  html: { enabled: true },
  spacer: { enabled: true },
  video: { enabled: false },
  carousel: { enabled: false },
  form: { enabled: false },
}
const options = {
  displayMode: 'email',
  user: { id: '1111' },
  tabs: { uploads: true, dev: false, images: true, audit: true },
  features: { imageEditor: true, undoRedo: true, userUploads: true, stockImages: { enabled: true, safeSearch: true, defaultSearchTerm: 'food' } },
}
const appearance = { theme: 'light', panels: { tools: { dock: 'right' } } }

function editorLoaded() {
  editorStatus.value = 'loaded'
  state.images = [
    {
      id: '1',
      location: 'https://picsum.photos/id/1/500',
      width: 300,
      height: 150,
      contentType: 'image/png',
      source: 'user',
    },
    {
      id: '2',
      location: 'https://picsum.photos/id/2/500',
      width: 400,
      height: 200,
      contentType: 'image/png',
      source: 'user',
    },
    {
      id: '3',
      location: 'https://picsum.photos/id/3/500',
      width: 500,
      height: 250,
      contentType: 'image/png',
      source: 'user',
    },
  ]
}

function editorReady() {
  editorStatus.value = 'ready'
  const unlayer = emailEditor.value.editor

  // FIXME : get design from database
  // ...
  // unlayer.loadDesign(state.design)

  // Image has beed uploaded
  unlayer.addEventListener('image:uploaded', function (data) {
    var image = data.image
    var url = image.url
    var width = image.width
    var height = image.height

    console.log('image:uploaded', data.image)

    state.images.unshift({
      id: Date.now(),
      location: url,
      width,
      height,
      contentType: 'image/png',
      source: 'user',
    })
  })

  // Image has been removed
  unlayer.registerCallback('image:removed', function (image, done) {
    console.log('image:removed', image)
    done()
  })

  // Uploading Image
  unlayer.registerCallback('image', (file, done) => {
    console.log('image', file)
    const blob = file.attachments?.[0]
    if (!blob) return done({ error: 'No file selected' })

    const reader = new FileReader()
    reader.onload = () => {
      done({ progress: 0, url: reader.result })
    }
    reader.onerror = () => done({ error: 'Could not read file' })
    reader.readAsDataURL(blob)

    // FIXME : upload image to server
    setTimeout(() => {
      done({ progress: 100, url: reader.result })
    }, 500)
    // unlayer.reloadProvider('userUploads')
    // ...
  })

  // User uploaded image galery
  unlayer.registerProvider('userUploads', (params, done) => {
    console.log('userUploads:', params)

    const { page = 1, searchText = '' } = params
    params.source = (params.source || []).filter(Boolean)

    console.log(state.images)

    // FIXME : get images galery from server
    done(JSON.parse(JSON.stringify(state.images)), { page, perPage: state.images.length, searchText }) // TODO : _.cloneDeep
    // ...
  })
}

function exportHtml() {
  emailEditor.value.editor.exportHtml((data) => {
    const { design, html } = data
    state.design = design
    state.html = html

    console.log('exportHtml', data)

    // FIXME : sage design into database
  })
}
</script>

<style lang="css" scoped>
.container {
  display: flex;
  height: 100%;
}
.unlayer-editor {
  width: 100%;
}
</style>
