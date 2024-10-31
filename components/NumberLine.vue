<template>
  <div class="h-screen relative overflow-hidden font-Excalifont">
    <div class="absolute bottom-12 left-4 mb-4 space-x-2">
      <button @click="refresh" class="px-4 py-2 bg-blue-500 text-white rounded">Återställ</button>
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
            <div :class="[numberLabelClass, isSelected(n) ? 'bg-blue-200 border border-blue-500 rounded-md' : '']" @click="selectNumber(n)">{{ n }}</div>
          </div>

          <!-- Ellipses around selected numbers -->
          <Ellipse
            v-if="selectedNumbers[0] === n"
            :width="20"
            :height="20"
            color="green"
            :strokeWidth="2"
            class="absolute pointer-events-none"
            :style="{
              left: vertical ? '37%' : '37%',
              top: vertical ? '50%' : '50%',
              transform: 'translate(-50%, -50%)'
            }"
          />
          <Ellipse
            v-if="selectedNumbers[1] === n"
            :width="20"
            :height="20"
            color="blue"
            :strokeWidth="2"
            class="absolute pointer-events-none"
            :style="{
              left: vertical ? '37%' : '37%',
              top: vertical ? '50%' : '50%',
              transform: 'translate(-50%, -50%)'
            }"
          />

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
import Ellipse from '@/components/Ellipse.vue';

export default {
  data() {
    return {
      numbers: Array.from({ length: 201 }, (_, i) => i - 100),
      vertical: false,
      selectedNumbers: [null, null], // Store two distinct selected numbers
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
        'text-sm text-gray-800 text-center transform cursor-pointer select-none hover:bg-gray-200 hover:rounded-md',
        this.vertical ? 'my-4 ml-1 w-6' : 'mt-16 w-6 transform -translate-x-1/2',
      ];
    },
    connectorLineClass() {
      return [
        'absolute bg-gray-400',
        this.vertical ? 'left-[37%] w-0.5 h-[calc(100%+3rem)]' : 'top-1/2 -translate-y-1/2 h-0.5 w-[calc(100%+3rem)]'
      ];
    }
  },
  methods: {
    selectNumber(n) {
      const index = this.selectedNumbers.indexOf(n);
      
      if (index === -1) {
        // If n is not already selected, add it to the first available spot
        const emptyIndex = this.selectedNumbers.indexOf(null);
        if (emptyIndex !== -1) {
          this.selectedNumbers[emptyIndex] = n; // Direct assignment
        }
      } else {
        // If n is already selected, remove it
        this.selectedNumbers[index] = null; // Direct assignment
      }

      console.log('Selected Numbers:', this.selectedNumbers); // Debugging line
    },
    isSelected(n) {
       return this.selectedNumbers && this.selectedNumbers.includes(n);
    },
    refresh() {
      this.centerScroll();
      this.selectedNumbers = [null, null];

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
.font-Excalifont {
  font-family: 'Excalifont', sans-serif;
}

/* Apply the custom font to the entire component */
:global(.font-Excalifont) {
  font-family: 'Excalifont', sans-serif;
}
</style>
