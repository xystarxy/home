<template>
  <section :class='!this.$store.state.app.screenfull?"app-main":"app-main fullscreen"' >
    <transition name="fade-transform" mode="out-in" >
      <router-view :key="key" />
    </transition>
  </section>
</template>

<script>
export default {
  name: "AppMain",
  computed: {
    cachedViews() {
      return this.$store.state.tagsView.cachedViews;
    },
    key() {
      return this.$route.path;
    }
  }
};
</script>

<style scoped>
.app-main {
  /*50 = navbar  */
  min-height: calc(100vh - 50px);
  width: 100%;
  position: relative;
  overflow: hidden;
}
.fixed-header + .app-main {
  padding-top: 50px;
}
.app-main.fullscreen {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 1002;
  bottom: 0;
  background-color: #00070f;
}
</style>

<style lang="scss">
// fix css style bug in open el-dialog
.el-popup-parent--hidden {
  .fixed-header {
    padding-right: 15px;
  }
}
</style>
