<!-- pages/index.vue -->
<template>
    <div class="page-root">
      <NumberLine />
    </div>
  </template>
  
  <script>
  import NumberLine from '~/components/NumberLine.vue'
  import Calculator from '~/components/Calculator.vue';
  
  
  export default {
    components: {
      NumberLine,
      Calculator,
    }
  }
  </script>
  
  <style scoped>
.page-root {
  user-select: none;
}
</style>