<template>
  <div class="h-screen relative overflow-hidden font-Excalifont">
    <div class="absolute bottom-12 left-4 mb-4 space-x-2">
      <button @click="refresh" class="px-4 py-2 bg-blue-500 text-white rounded">Återställ</button>
      <button @click="toggleOrientation" class="px-4 py-2 bg-blue-500 text-white rounded">Snurra</button>
    </div>
    <!-- Number line -->
    <div :class="numberLineClass">
   
      <div class="flex items-center" :class="orientationClass">
        <div v-for="(n, index) in displayedNumbers" :key="n" class="relative flex items-center">
          <!-- Container for elements in the numberline -->
          <div v-if="selectedNumbers[0] == n" 
              class="absolute" 
              :style="{
              width: vertical ? '200px' : arrowWidth + 'px',
              height: vertical ? arrowHeight + 'px' : '200px',
              bottom: vertical ? '0' : 'initial',
              right: vertical ? '0' : 'initial'
                }"
                >
          <Arrow
            v-if="selectedNumbers[0] < selectedNumbers[1] && selectedNumbers[0] !== null && selectedNumbers[1] !== null"
            :start="vertical ? [100,25] : [25, 70]"
            :end="vertical ? [100, arrowHeight-25] : [arrowWidth-25, 70]" 
            :roughness="1"
            :strokeWidth="3"  
            :width="vertical ? 200 : arrowWidth"  
            :height="vertical ? arrowHeight : 200" 
            :vertical="vertical"
            :strokeColor="'green'"
          />
          <div v-if="selectedNumbers[0] < selectedNumbers[1] && selectedNumbers[0] !== null && selectedNumbers[1] !== null" 
           class="absolute" 
           :style="{
             left: vertical ? '30px' : `${(arrowWidth / 2)}px`, 
             top: vertical ? `${(arrowHeight / 2)+10}px` : '10px',
             transform: vertical ? 'translateY(-100%)' : 'translateX(-50%)',
             zIndex: 10,
           }">
        <span class="bg-white text-gray-800 p-1 rounded shadow">
          + {{ Math.abs(selectedNumbers[1] - selectedNumbers[0]) }}
        </span>
      </div>
         </div>
         <div v-if="selectedNumbers[1] == n" 
              class="absolute" 
              :style="{
              width: vertical ? '200px' : arrowWidth + 'px',
              height: vertical ? arrowHeight + 'px' : '200px',
              bottom: vertical ? '0' : 'initial',
              right: vertical ? '0' : 'initial'
                }"
                >
                <Arrow v-if="selectedNumbers[0] > selectedNumbers[1] && selectedNumbers[0] !== null && selectedNumbers[1] !== null"
            :start="vertical ? [100,25] : [25, 70]"
            :end="vertical ? [100, arrowHeight-25] : [arrowWidth-25, 70]" 
            :roughness="1"
            :strokeWidth="3"  
            :width="vertical ? 200 : arrowWidth"  
            :height="vertical ? arrowHeight : 200" 
            :vertical="vertical"
            :strokeColor="'red'"
          />
          <div v-if="selectedNumbers[0] > selectedNumbers[1] && selectedNumbers[0] !== null && selectedNumbers[1] !== null" 
           class="absolute" 
           :style="{
             left: vertical ? '30px' : `${(arrowWidth / 2)}px`, 
             top: vertical ? `${(arrowHeight / 2)+10}px` : '10px',
             transform: vertical ? 'translateY(-100%)' : 'translateX(-50%)',
             zIndex: 10,
           }">
        <span class="bg-white text-gray-800 p-1 rounded shadow">
          - {{ Math.abs(selectedNumbers[1] - selectedNumbers[0]) }}
        </span>
      </div>
          </div>
          <div class="flex items-center mx-6">
            <!-- Marker line -->
            <div :class="markerLineClass">
            </div>
            <!-- Number label -->
            <div
              :ref="'label-' + n"
              :class="[numberLabelClass, isSelected(n) ? 'bg-blue-200 border border-blue-500 rounded-md' : '']"
              @click="selectNumber(n)"
              style="z-index: 10;"
            >
              {{ n }}
            </div>
            <!-- Ellipses around selected numbers -->
            <Ellipse
              v-if="selectedNumbers[0] === n"
              :width="20"
              :height="20"
              color="green"
              :strokeWidth="2"
              class="absolute pointer-events-none"
              :style="{ left: vertical ? '37%' : '37%', top: vertical ? '50%' : '50%', transform: 'translate(-50%, -50%)' }"
            />
            <Ellipse
              v-if="selectedNumbers[1] === n"
              :width="20"
              :height="20"
              color="blue"
              :strokeWidth="2"
              class="absolute pointer-events-none"
              :style="{ left: vertical ? '37%' : '37%', top: vertical ? '50%' : '50%', transform: 'translate(-50%, -50%)' }"
            />
            <!-- Line between markers -->
            <div
              v-if="index < displayedNumbers.length - 1"
              :class="connectorLineClass">  
              </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Ellipse from '@/components/Ellipse.vue';
import Arrow from '@/components/Arrow.vue';

export default {
  components: {
    Ellipse,
    Arrow,
  },
  data() {
    return {
      numbers: Array.from({ length: 201 }, (_, i) => i - 100),
      vertical: false,
      selectedNumbers: [null, null],
    };
  },
  computed: {
    displayedNumbers() {
      return this.vertical ? this.numbers.slice().reverse() : this.numbers;
    },
    numberLineClass() {
      return [
        'h-screen flex overflow-auto',
        this.vertical ? 'flex-col items-center' : 'items-center whitespace-nowrap overflow-x-scroll',
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
        this.vertical ? 'left-[37%] w-0.5 h-[calc(100%+3rem)]' : 'top-1/2 -translate-y-1/2 h-0.5 w-[calc(100%+3rem)]',
      ];
    },
    arrowWidth() {
      // Calculate the width based on the difference between selected numbers
      return this.selectedNumbers[1] !== null && this.selectedNumbers[0] !== null 
        ? Math.abs(this.selectedNumbers[1] - this.selectedNumbers[0]) * 73 + 50 // Adjust multiplier as needed
        : 10; // Default width if no numbers are selected
    },
    arrowHeight() {
      return this.selectedNumbers[1] !== null && this.selectedNumbers[0] !== null 
        ? Math.abs(this.selectedNumbers[1] - this.selectedNumbers[0]) * 52 + 50 // Adjust multiplier as needed
        : 10; // Default width if no numbers are selected
    },
  },
  methods: {
    selectNumber(n) {
      const index = this.selectedNumbers.indexOf(n);
      if (index === -1) {
        const emptyIndex = this.selectedNumbers.indexOf(null);
        if (emptyIndex !== -1) {
          this.selectedNumbers[emptyIndex] = n;
        }
      } else {
        this.selectedNumbers[index] = null;
      }
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
  },
};
</script>
