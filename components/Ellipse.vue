<template>
    <div ref="ellipseContainer" :style="{ width: width + 'px', height: height + 'px' }"></div>
  </template>
  
  <script>
  import rough from 'roughjs/bundled/rough.esm';
  
  export default {
    props: {
      width: { type: Number, default: 60 },
      height: { type: Number, default: 40 },
      color: { type: String, default: 'blue' },
      strokeWidth: { type: Number, default: 2 },
    },
    mounted() {
      if (process.client) {
        this.drawEllipse();
      }
    },
    beforeDestroy() {
      if (this.$refs.ellipseContainer) {
        this.$refs.ellipseContainer.innerHTML = '';
      }
    },
    methods: {
      drawEllipse() {
        if (!this.$refs.ellipseContainer) return;
  
        const svg = document.createElementNS('http://www.w3.org/2000/svg', 'svg');
        svg.setAttribute('width', this.width);
        svg.setAttribute('height', this.height);
        this.$refs.ellipseContainer.appendChild(svg);
  
        const rc = rough.svg(svg);
        const sketchyEllipse = rc.ellipse(this.width / 2, this.height / 2, this.width - 2, this.height - 2, {
          stroke: this.color,
          strokeWidth: this.strokeWidth,
          fill: this.computeFillColor(this.color),
          fillStyle: 'solid', // Makes the fill less rough
          roughness: 1, 
          bowing: 1, 
        });
  
        svg.appendChild(sketchyEllipse);
      },
      computeFillColor(color) {
        const shades = {
          blue: 'rgba(173, 216, 230, 0.5)',
          red: 'rgba(255, 182, 193, 0.5)',
          green: 'rgba(144, 238, 144, 0.5)',
          // Add more shades as needed
        };
        return shades[color] || 'rgba(173, 216, 230, 0.5)'; // Default to light blue if not found
      }
    },
  };
  </script>
  
  <style scoped>
  .canvas-container {
    width: 100%;
    height: 100%;
  }
  </style>
  