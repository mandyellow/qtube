<template>
  <q-layout view="lHh Lpr lFf">
    <q-layout-header>
      <q-tabs
        color="purple"
      >
        <q-route-tab slot="title" to="/index" icon="get_app" replace label="Download" />
        <q-route-tab slot="title" to="/videos" icon="perm_media" replace label="Videos" />
        <q-route-tab slot="title" to="/musics" icon="queue_music" replace label="Musics" />
        <q-route-tab slot="title" :to="path" icon="theaters" replace label="Player" />
        <q-tab class="updates" slot="title" :count="updatesAvailabe" @click="update()" icon="settings" replace label="Updates" />
        <q-tab class="minimize" slot="title" @click="minimizeApp()" icon="minimize" replace label="Minimize" />
        <q-tab class="absolute-right" slot="title" @click="closeApp()" icon="power_settings_new" replace label="Poweroff" />
      </q-tabs>
    </q-layout-header>

    <q-page-container class="background">
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { ipcRenderer } from 'electron'

export default {
  name: 'LayoutDefault',
  data () {
    return {
      path: {
        name: 'player',
        params: {
          src: '',
          img: ''
        }
      },
      updatesAvailabe: null
    }
  },
  mounted () {
    this.videoSelected()
    this.initialize()
    setTimeout(() => {
      this.checkUpdates()
    }, 1000)
  },
  methods: {
    closeApp () {
      ipcRenderer.send('close-app')
    },
    minimizeApp () {
      ipcRenderer.send('minimize')
    },
    videoSelected () {
      const selected = JSON.parse(window.localStorage.getItem('media'))
      if (selected) {
        this.path.params.src = selected.src
        this.path.params.img = selected.thumbnail
      }
    },
    initialize () {
      ipcRenderer.sendSync('createYtDownFolder')
      ipcRenderer.sendSync('createVideosFolder')
      ipcRenderer.sendSync('createMusicFolder')
      ipcRenderer.sendSync('createDatabaseFolder')
      ipcRenderer.sendSync('createFileDatabase')
    },
    checkUpdates () {
      ipcRenderer.on('updateReady', function (event, text) {
        this.updatesAvailabe = '1'
      })
    },
    update () {
      ipcRenderer.send('quitAndInstall')
    }
  }
}
</script>

<style>
::-webkit-scrollbar {
display: none;
}
.minimize {
  margin-left: 0em;
}

.updates {
  margin-left: 12em;
}
.background{
  background: #fff url(../statics/darkrola.jpeg);
  /* Full height */
  height: 100%;
  /* Center and scale the image nicely */
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}
</style>
