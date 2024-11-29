<script setup lang="ts">
import { computed, ref } from 'vue';

const countdown = ref()
const interval = setInterval(updateCountdown, 1000)

const props = defineProps({
  item: Object,
  server: String,
})

const getThumbnail = () => {
  if (!props['server'] || !props['item']?.defender_id)
    return "";

  const server = props['server']
  const id = props['item'].defender_id
  let url
  if (server == "sr")
    url = "https://image.evepc.163.com/Alliance/" + id + "_128.png";
  else if (server == "tq")
    url = "https://images.evetech.net/alliances/" + id + "/logo?size=128";
  else if (server == "if")
    url = "https://image.evepc.163.com/Alliance/" + id + "_128.png?datasource=infinity";
  else
    return ""

  return {
    backgroundImage:
      `linear-gradient(rgba(0,0,0,0.75), rgba(0,0,0,0.9)), url(${url})`,
    backgroundPosition: "center",
    backgroundSize: "cover",
  };
}

const type = computed(() => {
  if (props.item?.event_type == "ihub_defense")
    return  '主权建筑'
  else if (props.item?.event_type == "tcu_defense")
    return '基础设置'
  else if (props.item?.event_type == "station_defense")
    return '空间站'
  else if (props.item?.event_type == "station_freeport")
    return '自由港'
})

const started = computed(() => {
  return (new Date() > new Date(props.item?.start_time))
})

function updateCountdown() {
  const now = new Date().getTime()
  const time_left = (new Date(props.item?.start_time).getTime()) - now
  const days = Math.floor(time_left / (1000 * 60 * 60 * 24));
  const hours = Math.floor((time_left % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
  const minutes = Math.floor((time_left % (1000 * 60 * 60)) / (1000 * 60));
  const seconds = Math.floor((time_left % (1000 * 60)) / 1000);

  countdown.value = `${days ? days + '天' : ''}${hours ? hours + '小时' : ''}${minutes ? minutes + '分钟' : ''}${seconds ? seconds + '秒' : ''}`

  if (time_left <= 0) {
    clearInterval(interval)
    countdown.value = undefined
  }
}

updateCountdown()
</script>

<template>
  <v-sheet :elevation="7" :width="180" class="text-center ma-2 ma-md-4" :style="getThumbnail()">
    <h3 class="highlight">{{ item?.system_name + ' ' + type}}</h3>
    <a :href="`https://evehu.org/${server}/alliance/${item?.defender_id}`" target="_blank"><h3 class="text-center">{{ item?.alliance_name }}</h3></a>

    <v-progress-linear
      v-if="started"
      color="deep-orange"
      height="20"
      :model-value="item?.attackers_score * 100"
      striped
    >
    {{ item?.attackers_score * 100 + '%' }}
    </v-progress-linear>
    <h4 v-else class="font-weight-bold">
      {{ new Date(item?.start_time).toLocaleString('zh') }}
    </h4>

    <p v-if="countdown">距开始还有：</p>
    <p v-if="countdown" class="countdown">{{ countdown }}</p>
    <p v-else>进攻方进度</p>
  </v-sheet>
</template>

<style lang="css" scoped>
.v-sheet {
  color: #FEFEFE;
}
</style>