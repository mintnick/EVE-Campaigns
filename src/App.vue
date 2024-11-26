<script setup lang="ts">
import { ref, onMounted } from 'vue'
import ServerSection from './components/ServerSection.vue';

const loading = ref(true)
const drawer = ref(false)
const sr_data = ref()
const tq_data = ref()
const if_data = ref()

const sr_url = "https://eve-forge-api.nickning.app/sr/campaigns"
const tq_url = "https://eve-forge-api.nickning.app/tq/campaigns"
const if_url = "https://eve-forge-api.nickning.app/if/campaigns"

const other_list = [
  { title: 'EVE 户', value: 'https://evehu.org'},
  { title: '设置文件管理', value: 'https://nickning.app/esm'},
  { title: 'AT 阵容模拟', value: 'https://atdraft.evehu.org'},
]

const link_list = [
  { title: '捐赠', value: 'https://nickning.app/donate'},
  { title: 'GitHub', value: 'https://github.com/mintnick/EVEHu-Vue'},
]

async function fetchData() {
  try {
    let response = await fetch(sr_url)
    if (response.ok)
      sr_data.value = await response.json()

    response = await fetch(tq_url)
    if (response.ok)
      tq_data.value = await response.json()

    response = await fetch(if_url)
    if (response.ok)
      if_data.value = await response.json()
  } catch (error) {
    console.log('Fetch error: ', error)
  } finally {
    loading.value = false
  }
}

onMounted(async () => {
  await fetchData()
})
</script>

<template>
  <v-app id="v-app" class="d-flex">
    <v-app-bar :elevation="2" rounded density="compact" style="opacity: 90%; overflow: visible !important;">
      <v-app-bar-nav-icon
        @click.stop="drawer = !drawer"
      ></v-app-bar-nav-icon>

      <v-app-bar-title class="d-none d-sm-block">
        <a href="https://sov.evehu.org" class="text-h5">EVE 戎</a>
      </v-app-bar-title>

      <v-spacer></v-spacer>

      <v-btn icon href="https://nickning.app/donate" target="_blank">
        <v-icon>mdi-hand-coin-outline</v-icon>
      </v-btn>
      <v-btn icon href="https://github.com/mintnick/EVE-Campaigns/" target="_blank">
        <v-icon>mdi-github</v-icon>
      </v-btn>
    </v-app-bar>

    <v-navigation-drawer
      v-model="drawer"
      temporary
      class="text-center"
      color="rgba(255, 255, 255, 0.8)"
      width="150"
    >
      <v-list>
        <a v-for="item in other_list" :href="item.value" target="_blank">
          <v-list-item>{{ item.title }}</v-list-item>
        </a>
      </v-list>
      <v-divider />
      <v-list>
        <a v-for="item in link_list" :href="item.value" target="_blank">
          <v-list-item>{{ item.title }}</v-list-item>
        </a>
      </v-list>
    </v-navigation-drawer>

    <v-main id="main" class="d-flex flex-column align-center mt-1">
      <div v-if="loading" class="lds-dual-ring"></div>
      <ServerSection v-if="sr_data.length" title="晨曦" :data="sr_data" server="sr" />
      <ServerSection v-if="tq_data.length" title="宁静" :data="tq_data" server="tq"/>
      <ServerSection v-if="if_data.length" title="曙光" :data="if_data" server="if"/>
    </v-main>
  </v-app>
</template>

<style scoped>
#v-app {
  background-image: linear-gradient(rgba(0,0,0,0.7), rgba(0,0,0,0.7)), url('/wallpaper.jpg');
  background-position: center;
  background-size: cover;
}

#app a {
  color: #0E0E0E;
}
</style>
