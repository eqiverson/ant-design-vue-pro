<template>
  <a-config-provider :locale="locale">
    <div id="app">
      <router-view v-if="isReloadAlive"></router-view>
    </div>
  </a-config-provider>

</template>

<script>
import { domTitle, setDocumentTitle } from '@/utils/domUtil'
import { i18nRender } from '@/locales'



export default {

  provide() {
    return {
      reload: this.reload
    }
  },

  data () {
    return {
      isReloadAlive : true
    }
  },
  computed: {
    locale () {
      // 只是为了切换语言时，更新标题
      const { title } = this.$route.meta
      title && (setDocumentTitle(`${i18nRender(title)} - ${domTitle}`))

      return this.$i18n.getLocaleMessage(this.$store.getters.lang).antLocale
    }
  },
  methods: {
    reload() {
      this.isReloadAlive = false;
      this.$nextTick(function () {
        this.isReloadAlive = true;
      })
    },


  },

  }
</script>
