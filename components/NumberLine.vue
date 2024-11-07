<template>
  <div class="h-screen relative overflow-hidden font-Excalifont">
    <Calculator @calculationResult="handleCalculationResult" />
    <!-- Buttons -->
    <div class="absolute bottom-6 left-4 mb-4 flex gap-4" style="z-index: 10;">
      <!-- Scale Minus Button -->
      <ControlButton 
        :iconName="vertical ? 'verticalminus' : 'horizontalminus'"
        @click="scaleMinus"
      />

      <!-- Scale Plus Button -->
      <ControlButton 
        :iconName="vertical ? 'verticalplus' : 'horizontalplus'"
        @click="scalePlus"
      />

      <!-- Toggle Orientation Button -->
      <ControlButton 
        :iconName="vertical ? 'arrowVertical' : 'arrowHorizontal'"
        @click="toggleOrientation"
      />

      <!-- Refresh Button -->
      <ControlButton 
        iconName="refresh"
        @click="refresh"
      />
    </div>
    <!-- Number line -->
    <div :class="numberLineClass">
      <div class="flex items-center" :class="orientationClass">
        <div v-for="(n, index) in displayedNumbers" :key="n" class="relative flex items-center">
          <!-- Arrow logic -->
          <div v-if="selectedNumbers[0] === n " class="absolute" :style="getArrowStyle(0)">
            <Arrow
              v-if="shouldShowArrow(0)"
              :start="arrowStart"
              :end="getArrowEnd(0)"
              :roughness="1"
              :strokeWidth="3"  
              :width="getArrowWidth(0)"  
              :height="getArrowHeight(0)" 
              :vertical="vertical"
              :strokeColor="'green'"
            />
            <div v-if="shouldShowDifference(0)" 
              class="absolute" 
              :style="getDifferenceStyle(0)"
            >
              <span class="bg-white text-gray-800 p-1 rounded shadow">
                + {{ getDifference }}
              </span>
            </div>
          </div>
          
          <div v-if="selectedNumbers[1] === n" class="absolute" :style="getArrowStyle(1)">
            <Arrow
              v-if="shouldShowArrow(1)"
              :start="arrowStart"
              :end="getArrowEnd(1)"
              :roughness="1"
              :strokeWidth="3"  
              :width="getArrowWidth(1)"  
              :height="getArrowHeight(1)" 
              :vertical="vertical"
              :strokeColor="'red'"
            />
            <div v-if="shouldShowDifference(1)" 
              class="absolute" 
              :style="getDifferenceStyle(1)"
            >
              <span class="bg-white text-gray-800 p-1 rounded shadow">
                - {{ getDifference }}
              </span>
            </div>
          </div>

          <div class="flex items-center mx-6">
            <!-- Zero marker -->
            <div v-if="n === 0"
              class="absolute border border-gray-400 rounded pointer-events-none"
              :style="zeroMarkerStyle"
            ></div>
            
            <!-- Marker line -->
            <div :class="markerLineClass"></div>
            
            <!-- Number label -->
            <div
              :ref="'label-' + n"
              :class="getNumberLabelClass(n)"
              @click="selectNumber(n)"
              style="z-index: 10;"
            >
              {{ n }}
            </div>

            <!-- Selection indicators -->
            <Ellipse
              v-for="(selIndex, i) in getSelectionIndices(n)"
              :key="i"
              :width="20"
              :height="20"
              :color="selIndex === 0 ? 'green' : 'blue'"
              :strokeWidth="2"
              class="absolute pointer-events-none"
              :style="ellipseStyle"
            />

            <!-- Connector line -->
            <div v-if="index < displayedNumbers.length - 1" :class="connectorLineClass"></div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { defineComponent } from 'vue';
import Ellipse from '@/components/Ellipse.vue';
import Arrow from '@/components/Arrow.vue';
import Icons from '@/components/Icons.vue';
import ControlButton from './ControlButton.vue';
import Calculator from './Calculator.vue';

export default defineComponent({
  name: 'NumberLine',
  components: {
    Ellipse,
    Arrow,
    Icons,
    ControlButton,
    Calculator,
  },
  
  data() {
    return {
      numbers: Array.from({ length: 201 }, (_, i) => i - 100),
      scale: 1,
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

    connectorLineClass() {
      return [
        'absolute bg-gray-400',
        this.vertical ? 'left-[37%] w-0.5 h-[calc(100%+3rem)]' : 'top-1/2 -translate-y-1/2 h-0.5 w-[calc(100%+3rem)]',
      ];
    },

    zeroMarkerStyle() {
      return { 
        width: this.vertical ? '50px' : '1px',
        height: this.vertical ? '1px' : '50px',
        left: this.vertical ? '20px' : 'auto',
        zIndex: -1  
      };
    },

    ellipseStyle() {
      return { 
        left: this.vertical ? '37%' : '37%', 
        top: this.vertical ? '50%' : '50%', 
        transform: 'translate(-50%, -50%)' 
      };
    },

    arrowStart() {
      return this.vertical ? [100, 25] : [25, 70];
    },

    getDifference() {
      return Math.abs(this.selectedNumbers[1] - this.selectedNumbers[0]);
    }
  },

  methods: {
    handleCalculationResult(calculation) {
      if (calculation.includes('/') || calculation.includes('*')) {
        console.warn('Skipping calculation with multiplication or division:', calculation);
        return; // Exit the function early
      }

      // Regular expression to handle both positive and negative operands/results
      const match = calculation.match(/([-]?\d*\.?\d*)\s*[\+\-]\s*([-]?\d*\.?\d*)\s*=\s*([-]?\d*\.?\d*)/);

      if (match) {
        const [ , firstOperand, , result] = match.map(s => s.trim());
        this.selectedNumbers[0] = parseFloat(firstOperand); // First operand
        this.selectedNumbers[1] = parseFloat(result);       // Calculation result
      } else {
        console.warn("Failed to parse calculation:", calculation);
      }
    },
    getArrowWidth(index) {
      return Math.abs(this.selectedNumbers[1] - this.selectedNumbers[0]) * 73 * (1 / this.scale) + 50;
    },

    getArrowHeight(index) {
      return Math.abs(this.selectedNumbers[1] - this.selectedNumbers[0]) * 52 * (1 / this.scale) + 50;
    },

    getArrowEnd(index) {
      return this.vertical 
        ? [100, this.getArrowHeight(index) - 25] 
        : [this.getArrowWidth(index) - 25, 70];
    },

    getArrowStyle(index) {
      if (this.vertical) {
          return {
            width: '200px',  // Fixed width for vertical orientation
            height: this.getArrowHeight(index) + 'px',  // Dynamic height for vertical
            bottom: '0',
            right: '0',
          };
        } else {
          return {
            width: this.getArrowWidth(index) + 'px',  // Dynamic width for horizontal
            height: '200px',  // Fixed height for horizontal orientation
            bottom: 'initial',
            right: 'initial',
          };
        }
    },

    getDifferenceStyle(index) {
      return {
        left: this.vertical ? '30px' : `${(this.getArrowWidth(index) / 2)}px`,
        top: this.vertical ? `${(this.getArrowHeight(index) / 2)+10}px` : '10px',
        transform: this.vertical ? 'translateY(-100%)' : 'translateX(-50%)',
        zIndex: 10,
      };
    },

    shouldShowArrow(index) {
      return (index === 0 && this.selectedNumbers[0] < this.selectedNumbers[1] && this.selectedNumbers[1] !== null) ||
             (index === 1 && this.selectedNumbers[0] > this.selectedNumbers[1] && this.selectedNumbers[0] !== null);
    },

    shouldShowDifference(index) {
      return this.selectedNumbers[0] !== null && 
             this.selectedNumbers[1] !== null && 
             ((index === 0 && this.selectedNumbers[0] < this.selectedNumbers[1]) ||
              (index === 1 && this.selectedNumbers[0] > this.selectedNumbers[1]));
    },

    getNumberLabelClass(n) {
      return [
        'text-sm text-gray-800 text-center transform cursor-pointer select-none hover:bg-gray-200 hover:rounded-md',
        this.vertical ? 'my-4 ml-1 w-6' : 'mt-16 w-6 transform -translate-x-1/2',
        this.isSelected(n) ? 'bg-blue-200 border border-blue-500 rounded-md' : ''
      ];
    },

    getSelectionIndices(n) {
      return this.selectedNumbers
        .map((num, index) => num === n ? index : null)
        .filter(index => index !== null);
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
      return this.selectedNumbers.includes(n);
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
});
</script>