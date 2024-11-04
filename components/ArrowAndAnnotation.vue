<template>
    <div class="absolute" :style="arrowStyle">
      <Arrow
        v-if="selectedNumbers[0] < selectedNumbers[1]"
        :start="arrowStart"
        :end="arrowEnd"
        :roughness="1"
        :strokeWidth="3"
        :width="arrowWidth"
        :height="arrowHeight"
        :vertical="vertical"
        :strokeColor="'green'"
      />
      <div v-if="selectedNumbers[0] < selectedNumbers[1]">
        <span class="bg-white text-gray-800 p-1 rounded shadow">
          + {{ Math.abs(selectedNumbers[1] - selectedNumbers[0]) }}
        </span>
      </div>
      <Arrow
        v-else
        :start="arrowStart"
        :end="arrowEnd"
        :roughness="1"
        :strokeWidth="3"
        :width="arrowWidth"
        :height="arrowHeight"
        :vertical="vertical"
        :strokeColor="'red'"
      />
      <div v-if="selectedNumbers[0] > selectedNumbers[1]">
        <span class="bg-white text-gray-800 p-1 rounded shadow">
          - {{ Math.abs(selectedNumbers[1] - selectedNumbers[0]) }}
        </span>
      </div>
    </div>
  </template>
  
  <script>
  import Arrow from '@/components/Arrow.vue';
  
  export default {
    props: {
      selectedNumbers: {
        type: Array,
        required: true,
      },
      vertical: {
        type: Boolean,
        required: true,
      },
      scale: {
        type: Number,
        required: true,
      },
      index: {
        type: Number,
        required: true,
      },
    },
    computed: {
      arrowWidth() {
        return Math.abs(this.selectedNumbers[1] - this.selectedNumbers[0]) * 73 + 50; // Adjust multiplier as needed
      },
      arrowHeight() {
      return Math.abs(this.selectedNumbers[1] - this.selectedNumbers[0]) * 20; // Adjust multiplier as needed
    },
    arrowStart() {
      const start = this.selectedNumbers[0] * this.scale;
      return this.vertical ? { x: 0, y: start } : { x: start, y: 0 };
    },
    arrowEnd() {
      const end = this.selectedNumbers[1] * this.scale;
      return this.vertical ? { x: 0, y: end } : { x: end, y: 0 };
    },
    arrowStyle() {
      return {
        left: this.vertical ? '50%' : `${Math.min(this.selectedNumbers[0], this.selectedNumbers[1]) * this.scale}px`,
        top: this.vertical ? `${Math.min(this.selectedNumbers[0], this.selectedNumbers[1]) * this.scale}px` : '50%',
        transform: this.vertical ? 'translate(-50%, 0)' : 'translate(0, -50%)',
      };
    },
  },
};
</script>