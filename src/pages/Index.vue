<template>
  <q-page :class="ready ? 'bg-white' : 'flex flex-center transparent'">
    <div v-if="ready">
      {{imageURI}}
    </div>
    <q-separator/>
    <div v-if="ready">
      <button v-if="authorized" class="secondary push" @click="goScan()">Go Scan</button>
    </div>
    <q-separator/>
    <div v-if="!ready" class="container">
      <q-img
        :src="rect"
      />
      <q-separator/>
      <q-btn color="primary" label="Cancel" @click="cancelScan()"/>
    </div>
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  mounted () {
    this.prepDevice()
  },
  data () {
    return {
      ready: true,
      imageURI: '',
      rect: 'statics/rect.png',
      authorized: false,
      selection: 'standard',
      selectOptions: [
        {
          label: 'Camera-thumbnail',
          value: 'camera-thmb'
        },
        {
          label: 'Standard',
          value: 'standard'
        }
      ],
      enableVisibility: 'hidden',
      backColor: 'transparent'
    }
  },
  methods: {
    prepDevice () {
      window.QRScanner.prepare(this.onDone)
    },
    onDone: function (err, status) {
      if (err) {
        alert('preparing: error code = ' + err.code)
      }
      if (status.authorized) {
        this.authorized = true
      } else if (status.denied || !status.authorized) {
        this.openSettings()
      } else {

      }
    },
    goScan: function () {
      this.authorized = false
      this.ready = false

      window.QRScanner.show()

      window.QRScanner.scan(this.displayContents)
    },
    displayContents: function (err, text) {
      if (err) {
        alert('scanning: error code = ' + err.code)
        if (err.name === 'SCAN_CANCELED') {
          alert('The scan was cancelled before a QR code was found.')
        }
      } else {
        alert(text)
        this.imageURI = text
      }
      window.QRScanner.destroy()
      this.authorized = true
      this.ready = true
    },
    cancelScan () {
      window.QRScanner.cancelScan()
      this.authorized = true
      this.ready = true
    },
    openSettings () {
      if (status.canOpenSettings) {
        if (confirm('Would you like to enable QR code scanning? You can allow camera access in your settings.')) {
          window.QRScanner.openSettings()
        }
      }
    }
  }
}
</script>
<style scoped lang="scss">
  .container {
    flex:1;
    align-items: 'center'
  }
</style>
