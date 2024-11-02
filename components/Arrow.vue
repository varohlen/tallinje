<template>
    <div ref="arrowContainer" class="arrow-container"></div>
</template>

<script>
import rough from 'roughjs/bundled/rough.esm';

export default {
    props: {
        start: {
            type: Array,
            default: () => [10, 90],
        },
        end: {
            type: Array,
            default: () => [90, 10],
        },
        strokeWidth: { type: Number, default: 2 },
        roughness: { type: Number, default: 1 },
        strokeColor: { type: String, default: 'blue' },
        height: { type: Number, default: 200 },  // New prop for height
        width: { type: Number, default: 1000 },  // New prop for width
        vertical: { type: Boolean, default: false } // New prop to indicate vertical orientation
    },

    mounted() {
        this.drawCurvedArrow();
    },

    watch: {
        start: 'drawCurvedArrow',
        end: 'drawCurvedArrow',
        vertical: 'drawCurvedArrow', // Watch vertical prop to redraw arrow
    },

    methods: {
        drawCurvedArrow() {
            const [startX, startY] = this.start;
            const [endX, endY] = this.end;

            const svgWidth = this.width;  // Use the width prop
            const svgHeight = this.height; // Use the height prop

            const svg = document.createElementNS('http://www.w3.org/2000/svg', 'svg');
            svg.setAttribute('width', svgWidth);
            svg.setAttribute('height', svgHeight);
            svg.setAttribute('viewBox', `0 0 ${svgWidth} ${svgHeight}`); // Use the fixed viewBox

            this.$refs.arrowContainer.innerHTML = '';
            this.$refs.arrowContainer.appendChild(svg);

            const rc = rough.svg(svg);

            // Adjust control point for curvature based on orientation
            const curvature = 50; // Fixed curvature height
            let controlX, controlY;

            if (this.vertical) {
                // For vertical arrows, curve to the left
                controlX = startX - curvature; // Control point's X is shifted left
                controlY = (startY + endY) / 2; // Control point's Y is centered
            } else {
                // For horizontal arrows, curve upwards
                controlX = (startX + endX) / 2; // Control point's X is centered
                controlY = Math.min(startY, endY) - curvature; // Control point's Y based on curvature
            }

            const path = rc.curve(
                [
                    [startX, startY],
                    [controlX, controlY],
                    [endX, endY],
                ],
                {
                    stroke: this.strokeColor,
                    strokeWidth: this.strokeWidth,
                    roughness: this.roughness,
                }
            );

            svg.appendChild(path);
        },
    },
};
</script>

<style scoped>
.arrow-container {
    position: relative;
}
</style>
