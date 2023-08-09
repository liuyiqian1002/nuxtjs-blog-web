<template>
  <div>
    <Header />
    <Nuxt />
    <Footer />
    <div style="position: fixed; top: 0;z-index: -1; background-color: #FFFAFA; width: 100%;">
      <vue-particles color="#dedede"></vue-particles>
    </div>
  </div>
</template>
<script>
import Header from '@/components/common/Header'
import Footer from '@/components/common/Footer'
export default {
  components: {
    Header,
    Footer
  },
}
</script>
<style lang="scss" scoped>
</style>
