<template>
  <div class="h-screen relative overflow-hidden">
    <div class="absolute bottom-12 left-4 mb-4 space-x-2">
      <button @click="refresh" class="px-4 py-2 bg-blue-500 text-white rounded">Refresh</button>
      <button @click="toggleOrientation" class="px-4 py-2 bg-blue-500 text-white rounded">Snurra</button>
    </div>

    <!-- Number line -->
    <div :class="numberLineClass">
      <div class="inline-flex items-center" :class="orientationClass">
        <div v-for="(n, index) in displayedNumbers" :key="n" class="relative flex items-center">
          <div class="flex items-center mx-6">    
            <!-- Marker line -->
            <div :class="markerLineClass"></div>
            <!-- Number label -->
            <div :class="numberLabelClass">{{ n }}</div>
          </div>
          
          <!-- Line between markers -->
          <div
            v-if="index < displayedNumbers.length - 1"
            :class="connectorLineClass"
          ></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      numbers: Array.from({ length: 101 }, (_, i) => i - 50),
      vertical: false,
    }
  },
  computed: {
    displayedNumbers() {
      return this.vertical ? this.numbers.slice().reverse() : this.numbers;
    },
    numberLineClass() {
      return [
        'h-screen flex overflow-auto',
        this.vertical ? 'flex-col items-center' : 'items-center whitespace-nowrap overflow-x-scroll'
      ];
    },
    orientationClass() {
      return this.vertical ? 'flex-col' : 'flex-row';
    },
    markerLineClass() {
      return this.vertical ? 'h-px w-10 bg-gray-400' : 'w-px h-10 bg-gray-400';
    },
    numberLabelClass() {
      return [
        'text-sm text-gray-800 text-center transform',
        this.vertical ? 'my-4 ml-1 w-6' : 'mt-16 w-6 transform -translate-x-1/2'
      ];
    },
    connectorLineClass() {
      return [
        'absolute bg-gray-400',
        this.vertical ? 'left-[37%] w-0.5 h-[calc(100%+3rem)]' : 'top-1/2 -translate-y-1/2 h-0.5 w-[calc(100%+3rem)]'
      ];
    },
  },
  methods: {
    refresh() {
      this.$router.go(0);
    },
    toggleOrientation() {
      this.vertical = !this.vertical;
      this.centerScroll();
    },
    centerScroll() {
      this.$nextTick(() => {
        const scrollContainer = this.$el.querySelector('.overflow-auto');
        if (this.vertical) {
          scrollContainer.scrollTop = (scrollContainer.scrollHeight / 2) - (window.innerHeight / 2);
        } else {
          scrollContainer.scrollLeft = (scrollContainer.scrollWidth / 2) - (window.innerWidth / 2);
        }
      });
    },
  },
  mounted() {
    this.centerScroll();
  }
}
</script>

<style scoped>
/* Add any additional styling here */
</style>
