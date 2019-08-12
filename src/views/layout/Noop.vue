<template>
    <router-view :key="key"/>
</template>

<script>
export default {
  name: 'Noop',
  computed: {
    key() {
      return this.$route.fullPath
    }
  }
}
</script>