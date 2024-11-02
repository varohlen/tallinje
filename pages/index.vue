<!-- pages/index.vue -->
<template>
    <div class="page-root">
      <NumberLine />
    </div>
  </template>
  
  <script>
  import NumberLine from '~/components/NumberLine.vue'
  
  
  export default {
    components: {
      NumberLine
    }
  }
  </script>
  
  <style scoped>
.page-root {
  user-select: none;
}
</style>