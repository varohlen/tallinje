<template>
    <div
      v-if="mode === 'calculator'"
      class="absolute z-50"
      :style="{
        bottom: `${position.bottom}px`,
        right: `${position.right}px`,
        zIndex: 20,
        cursor: dragging ? 'grabbing' : 'grab'
      }"
      @mousedown.prevent="startDragging"
    >
      <!-- Full Calculator UI -->
      <div class="w-64 mx-auto bg-gray-100 rounded-lg p-4 shadow-md">
        <!-- Minimize Button -->
        <button
          @click="minimize"
          class="absolute top-0 left-0 text-gray-600 border-gray-200 px-1"
        >
          X
        </button>
  
        <!-- Display Area -->
        <div class="text-right mb-4">
          <input
            type="text"
            v-model="currentInput"
            readonly
            class="w-full text-2xl p-3 bg-white border rounded-lg text-gray-800"
          />
        </div>
  
        <!-- Calculator Buttons -->
        <div class="grid grid-cols-4 gap-3">
          <ControlButton
            v-for="button in buttons"
            :key="button"
            :stringName="button"
            @click="handleClick(button)"
            class="py-3 px-6 bg-gray-200 text-xl rounded-lg hover:bg-gray-300 active:bg-gray-400 transition-colors"
          />
        </div>
      </div>
    </div>
  
    <div
      v-else
      class="absolute z-50"
      :style="{
        right: '0',
        bottom: '6rem',
        width: '2rem',
        height: '4rem',
        transform: 'translateX(0)',
      }"
    >
      <!-- Minimized Tray UI -->
      <div
        class="bg-gray-200 text-center py-2 px-1 cursor-pointer flex flex-col justify-between items-center"
        @click="restore"
      >
        <span>+</span>
        <span>-</span>
        <span>*</span>
        <span>/</span>
        <span>=</span>
      </div>
    </div>
  </template>
  
  <script>
  import { defineComponent } from 'vue';
  import ControlButton from '@/components/ControlButton.vue';
  import { useI18n } from '#imports';
  
  export default defineComponent({
    name: 'Calculator',
    components: {
      ControlButton,
    },
    props: {
      initialPosition: {
        type: Object,
        default: () => ({
          bottom: 20,
          right: 10,
        }),
      },
    },
    setup() {
      const { t, locale } = useI18n();
      return {
        t,
        locale,
      };
    },
  
    data() {
      return {
        currentInput: '',
        buttons: [
          '7', '8', '9', '/',
          '4', '5', '6', '*',
          '1', '2', '3', '-',
          '0', '.', '=', '+',
          'C'
        ],
        position: { ...this.initialPosition },
        dragging: false,
        dragStart: {
          x: 0,
          y: 0,
          right: 0,
          bottom: 0
        },
        mode: 'calculator',
      };
    },
    
    methods: {
      handleClick(button) {
        if (button === 'C') {
          this.currentInput = '';
        } else if (button === '=') {
          this.calculateResult();
        } else {
          if (this.currentInput.includes('=')) {
            const result = this.currentInput.split('=').pop().trim();
            this.currentInput = result + button;
          } else {
            this.currentInput += button;
          }
        }
      },
  
      calculateResult() {
        try {
          const result = eval(this.currentInput);
          const fullCalculation = `${this.currentInput} = ${result}`;
          this.currentInput = fullCalculation;
          this.$emit('calculationResult', fullCalculation);
        } catch (error) {
          this.currentInput = 'Error';
        }
      },
  
      startDragging(event) {
        // Prevent text selection during drag
        event.preventDefault();
        
        this.dragging = true;
        
        // Store initial mouse position and calculator position
        this.dragStart = {
          x: event.clientX,
          y: event.clientY,
          right: this.position.right,
          bottom: this.position.bottom
        };
  
        window.addEventListener('mousemove', this.onMouseMove);
        window.addEventListener('mouseup', this.stopDragging);
      },
  
      onMouseMove(event) {
        if (!this.dragging) return;
  
        // Calculate the delta (how far we've moved)
        const deltaX = this.dragStart.x - event.clientX;
        const deltaY = this.dragStart.y - event.clientY;
  
        // Update position based on the original position plus the delta
        const newRight = this.dragStart.right + deltaX;
        const newBottom = this.dragStart.bottom + deltaY;
  
        // Get calculator dimensions
        const calculatorWidth = this.$el.offsetWidth;
        const calculatorHeight = this.$el.offsetHeight;
  
        // Constrain to window bounds
        this.position.right = Math.min(
          Math.max(0, newRight),
          window.innerWidth - calculatorWidth
        );
        this.position.bottom = Math.min(
          Math.max(0, newBottom),
          window.innerHeight - calculatorHeight
        );
      },
  
      stopDragging() {
        this.dragging = false;
        window.removeEventListener('mousemove', this.onMouseMove);
        window.removeEventListener('mouseup', this.stopDragging);
      },
  
      minimize() {
        this.mode = 'tray';
      },
  
      restore() {
        this.mode = 'calculator';
      }
    },
  });
  </script>
  
  <style scoped>
  .absolute {
    user-select: none;
  }
  </style>