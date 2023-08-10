<template>
  <div>
    <Header />
    <Nuxt />
    <Footer />
    <div class="particles" style="position: fixed; top: 0;z-index: -1; background-color: #FFFAFA; width: 100%;">
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
.particles{
  background: url('../assets/imgs/sky.bg.jpg');
  background-repeat: no-repeat;
  background-size: 100% 100%;
}
</style>
