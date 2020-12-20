<template>
  <a-locale-provider :locale="locale">
    <div id="app">
      <router-view/><!--<sock/> -->
    </div>
  </a-locale-provider>
</template>
<script>
  import zhCN from 'ant-design-vue/lib/locale-provider/zh_CN'
  import enquireScreen from '@/utils/device'
//开发环境中不使用activeMQ，注释掉下行。
  //  import sock from './components/sock'
//  import { getAction } from './api/manage'

  export default {
    components: {
//      sock
    },
    data () {
      return {
        locale: zhCN,

      }
    },
    created () {
      let that = this
      enquireScreen(deviceType => {
        // tablet
        if (deviceType === 0) {
          that.$store.commit('TOGGLE_DEVICE', 'mobile')
          that.$store.dispatch('setSidebar', false)
        }
        // mobile
        else if (deviceType === 1) {
          that.$store.commit('TOGGLE_DEVICE', 'mobile')
          that.$store.dispatch('setSidebar', false)
        }
        else {
          that.$store.commit('TOGGLE_DEVICE', 'desktop')
          that.$store.dispatch('setSidebar', true)
        }

      })

    }
  }
</script>
<style>
  #app {
    height: 100%;
  }
</style>