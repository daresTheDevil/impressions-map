<template>
  <v-app dark>
    <v-navigation-drawer v-model="drawer" absolute temporary app>
      <v-list>
        <v-list-tile
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-tile-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-tile-action>
          <v-list-tile-content>
            <v-list-tile-title v-text="item.title" />
          </v-list-tile-content>
        </v-list-tile>
      </v-list>
    </v-navigation-drawer>
    <v-toolbar app>
      <v-toolbar-side-icon @click="drawer = !drawer" />
      <v-toolbar-title v-text="title" />
      <v-spacer />
      <v-btn color="warning" class="black--text" light @click="addFiles">
        Click to upload
        <v-icon>mdi-cloud-upload</v-icon>
      </v-btn>
      <!-- <v-sheet
        color="warning"
        width="280"
        class="pa-2 text-xs-center"
        light
        :class="dragging ? 'error' : 'warning'"
        @drop.stop.prevent="handleDragDropUpload"
        @dragenter.stop.prevent="dragging = true"
        @dragover.stop.prevent="dragging = true"
        @dragleave.stop.prevent="dragging = false"
        @click="addFiles"
      >
        Drop files here or click to upload
      </v-sheet> -->
    </v-toolbar>
    <v-content>
      <nuxt />
    </v-content>
    <input
      id="fileInput"
      ref="files"
      type="file"
      class="hidden"
      @change="upload"
    />
  </v-app>
</template>

<script>
import Papa from 'papaparse'

export default {
  components: {},
  data() {
    return {
      doc: null,
      dragging: false,
      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: 'apps',
          title: 'Welcome',
          to: '/'
        },
        {
          icon: 'bubble_chart',
          title: 'Inspire',
          to: '/inspire'
        }
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Impressions Map'
    }
  },
  methods: {
    upload(e) {
      const that = this
      const fileToLoad = event.target.files[0]
      const reader = new FileReader()
      reader.onload = fileLoadedEvent => {
        Papa.parse(fileLoadedEvent.target.result, {
          header: true,
          dynamicTyping: true,
          skipEmptyLines: true,
          complete(results) {
            console.warn('complete', results)
            that.doc = JSON.stringify(results.data, null, 2)
            // that.$store.commit('SET_MARKERS', that.doc)
            that.handleMarkers()
          },
          error(errors) {}
        })
      }
      reader.readAsText(fileToLoad)
    },
    handleMarkers() {
      const markers = JSON.parse(this.doc)
      console.log('doc', markers)
      const newMarkers = markers.map(function(d) {
        return {
          id: d.recId,
          position: [d.lat, d.lng],
          city: d.displayCity,
          state: d.displayState,
          impressions: d.performanceImpressions
        }
      })
      this.$store.commit('SET_MARKERS', newMarkers)
    },
    addFiles() {
      // called when file upload is clicked
      this.$refs.files.click()
    },
    handleDragDropUpload(e) {
      this.dragging = false
      const droppedFiles = e.dataTransfer.files
      for (let i = 0; i < droppedFiles.length; i++) {
        this.files.push(droppedFiles[i])
      }
    },
    onFilesAdded() {
      // const uploadedFiles = this.$refs.files.files
      // add files to array
      // for (let i = 0; i < uploadedFiles.length; i++) {
      //   const file = uploadedFiles[i]
      //   this.files.push(file)
      // }
    }
  }
}
</script>

<style>
input[type='file'] {
  position: absolute;
  top: -500px;
}
</style>
