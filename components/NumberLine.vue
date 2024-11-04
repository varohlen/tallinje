<template>
  <div class="h-screen relative overflow-hidden font-Excalifont">
    <!-- Buttons -->
    <div class="absolute bottom-12 left-4 mb-4 flex gap-4" style="z-index: 10;">
    <!-- Scale Minus Button -->
    <button class="bg-white hover:bg-gray-100 text-gray-800 py-2 px-2 border border-gray-400 rounded shadow" @click="scaleMinus">
      <Icons :iconName="vertical ? 'verticalminus' : 'horizontalminus'" strokeColor="black" width="2rem" height="2rem" roughness="0.2" stroke-width="0.5"/>
    </button>

    <!-- Scale Plus Button -->
    <button class="bg-white hover:bg-gray-100 text-gray-800 py-2 px-2 border border-gray-400 rounded shadow" @click="scalePlus">
      <Icons :iconName="vertical ? 'verticalplus' : 'horizontalplus'" strokeColor="black" width="2rem" height="2rem" roughness="0.2" stroke-width="0.5"/>
    </button>

    <!-- Toggle Orientation Button -->
    <button class="bg-white hover:bg-gray-100 text-gray-800 py-2 px-2 border border-gray-400 rounded shadow" @click="toggleOrientation">
      <Icons :iconName="vertical ? 'arrowVertical' : 'arrowHorizontal'" strokeColor="black" width="2rem" height="2rem" roughness="0.2" stroke-width="0.5"/>
    </button>

    <!-- Refresh Button -->
    <button class="bg-white hover:bg-gray-100 text-gray-800 py-2 px-2 border border-gray-400 rounded shadow" @click="refresh">
      <Icons iconName="refresh" strokeColor="black" width="2rem" height="2rem" roughness="0.2" stroke-width="0.5"/>
    </button>
    </div>

    <!-- Number line -->
    <div :class="numberLineClass">
      <div class="flex items-center" :class="orientationClass">
        <div v-for="(n, index) in displayedNumbers" :key="n" class="relative flex items-center">
          <!-- Arrow logic -->
          <div v-if="selectedNumbers[0] === n" class="absolute" :style="arrowStyle(0)">
            <Arrow
              v-if="selectedNumbers[0] < selectedNumbers[1] && selectedNumbers[1] !== null"
              :start="arrowStart(0)"
              :end="arrowEnd(0)"
              :roughness="1"
              :strokeWidth="3"  
              :width="arrowWidth(0)"  
              :height="arrowHeight(0)" 
              :vertical="vertical"
              :strokeColor="'green'"
            />
            <div v-if="selectedNumbers[0] < selectedNumbers[1] && selectedNumbers[0] !== null && selectedNumbers[1] !== null" 
           class="absolute" 
           :style="{
             left: vertical ? '30px' : `${(arrowWidth(0) / 2)}px`, 
             top: vertical ? `${(arrowHeight(0) / 2)+10}px` : '10px',
             transform: vertical ? 'translateY(-100%)' : 'translateX(-50%)',
             zIndex: 10,
           }">
        <span class="bg-white text-gray-800 p-1 rounded shadow">
          + {{ Math.abs(selectedNumbers[1] - selectedNumbers[0]) }}
        </span>
      </div>
          </div>
          <div v-if="selectedNumbers[1] === n" class="absolute" :style="arrowStyle(1)">
            <Arrow
              v-if="selectedNumbers[0] > selectedNumbers[1] && selectedNumbers[0] !== null"
              :start="arrowStart(1)"
              :end="arrowEnd(1)"
              :roughness="1"
              :strokeWidth="3"  
              :width="arrowWidth(1)"  
              :height="arrowHeight(1)" 
              :vertical="vertical"
              :strokeColor="'red'"
            />
            <div v-if="selectedNumbers[0] > selectedNumbers[1] && selectedNumbers[0] !== null && selectedNumbers[1] !== null" 
           class="absolute" 
           :style="{
             left: vertical ? '30px' : `${(arrowWidth(1) / 2)}px`, 
             top: vertical ? `${(arrowHeight(1) / 2)+10}px` : '10px',
             transform: vertical ? 'translateY(-100%)' : 'translateX(-50%)',
             zIndex: 10,
           }">
        <span class="bg-white text-gray-800 p-1 rounded shadow">
          - {{ Math.abs(selectedNumbers[1] - selectedNumbers[0]) }}
        </span>
      </div>
          </div>
          <div class="flex items-center mx-6">
            <div v-if="n === 0"
              class="absolute border border-gray-400 rounded pointer-events-none"
              :style="{ 
              width: vertical ? '50px' : '1px', /* Thinner width for vertical, height for horizontal */
              height: vertical ? '1px' : '50px', /* Thinner height for vertical, width for horizontal */
              left: vertical ? '20px' : 'auto',
              zIndex: -1  
              }">
        </div>
            <!-- Marker line -->
            <div :class="markerLineClass"></div>
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
            <div v-if="index < displayedNumbers.length - 1" :class="connectorLineClass"></div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Ellipse from '@/components/Ellipse.vue';
import Arrow from '@/components/Arrow.vue';
import Icons from '@/components/Icons.vue';

export default {
  components: {
    Ellipse,
    Arrow,
    Icons,
  },
  data() {
    return {
      numbers: Array.from({ length: 201 }, (_, i) => i - 100),
      scale: 1, // default scale
      scaleSteps: [0.01, 0.1, 0.5, 1, 2, 5, 10, 100],
      vertical: false,
      selectedNumbers: [null, null],
    };
  },
  computed: {
    displayedNumbers() {
      const scaledNumbers = this.numbers.map(num => {
        const scaledNumber = num * this.scale;
        return this.scale <= 0.1 ? parseFloat(scaledNumber.toFixed(2)) : scaledNumber;
      });
      return this.vertical ? scaledNumbers.slice().reverse() : scaledNumbers;
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
  },
  methods: {
    arrowWidth(index) {
      return Math.abs(this.selectedNumbers[1] - this.selectedNumbers[0]) * 73 * (1 / this.scale) + 50; // Adjust the formula as needed
    },
    arrowHeight(index) {
      return Math.abs(this.selectedNumbers[1] - this.selectedNumbers[0]) * 52 * (1 / this.scale) + 50; // Adjust the formula as needed
    },
    arrowStart(index) {
      return this.vertical ? [100, 25] : [25, 70];
    },
    arrowEnd(index) {
      return this.vertical ? [100, this.arrowHeight(index) - 25] : [this.arrowWidth(index) - 25, 70];
    },
    arrowStyle(index) {
      return {
        width: this.vertical ? '200px' : this.arrowWidth(index) + 'px',
        height: this.vertical ? this.arrowHeight(index) + 'px' : '200px',
        bottom: this.vertical ? '0' : 'initial',
        right: this.vertical ? '0' : 'initial',
      };
    },
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
    scaleMinus() {
      const currentIndex = this.scaleSteps.indexOf(this.scale);
      if (currentIndex > 0) {
        this.scale = this.scaleSteps[currentIndex - 1];
      }
    },
    scalePlus() {
      const currentIndex = this.scaleSteps.indexOf(this.scale);
      if (currentIndex < this.scaleSteps.length - 1) {
        this.scale = this.scaleSteps[currentIndex + 1];
      }
    },
  },
  mounted() {
    this.centerScroll();
  },
};
</script>

