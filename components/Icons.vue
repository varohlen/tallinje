<template>
    <div ref="iconContainer" class="icon-container"></div>
  </template>
  
  <script>
  import rough from 'roughjs/bundled/rough.esm';
  
  const icons = {
    arrowHorizontal: `
      <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-arrows" viewBox="0 0 16 16">
        <path d="M1.146 8.354a.5.5 0 0 1 0-.708l2-2a.5.5 0 1 1 .708.708L2.707 7.5h10.586l-1.147-1.146a.5.5 0 0 1 .708-.708l2 2a.5.5 0 0 1 0 .708l-2 2a.5.5 0 0 1-.708-.708L13.293 8.5H2.707l1.147 1.146a.5.5 0 0 1-.708.708z"/>
      </svg>
    `,
    arrowVertical: `
      <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-arrows-vertical" viewBox="0 0 16 16">
        <path d="M8.354 14.854a.5.5 0 0 1-.708 0l-2-2a.5.5 0 0 1 .708-.708L7.5 13.293V2.707L6.354 3.854a.5.5 0 1 1-.708-.708l2-2a.5.5 0 0 1 .708 0l2 2a.5.5 0 0 1-.708.708L8.5 2.707v10.586l1.146-1.147a.5.5 0 0 1 .708.708z"/>
      </svg>
    `,
    refresh:`<svg width="15" height="15" viewBox="0 0 15 15" fill="none" xmlns="http://www.w3.org/2000/svg">
  <path
    fill-rule="evenodd"
    clip-rule="evenodd"
    d="M12.8536 2.85355C13.0488 2.65829 13.0488 2.34171 12.8536 2.14645C12.6583 1.95118 12.3417 1.95118 12.1464 2.14645L7.5 6.79289L2.85355 2.14645C2.65829 1.95118 2.34171 1.95118 2.14645 2.14645C1.95118 2.34171 1.95118 2.65829 2.14645 2.85355L6.79289 7.5L2.14645 12.1464C1.95118 12.3417 1.95118 12.6583 2.14645 12.8536C2.34171 13.0488 2.65829 13.0488 2.85355 12.8536L7.5 8.20711L12.1464 12.8536C12.3417 13.0488 12.6583 13.0488 12.8536 12.8536C13.0488 12.6583 13.0488 12.3417 12.8536 12.1464L8.20711 7.5L12.8536 2.85355Z"
    fill="currentColor"
  />
</svg>

    `
    ,
    horizontalplus:`<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-arrows" viewBox="0 0 16 16">
  <path d="M1.146 10.354a.5.5 0 0 1 0-.708l2-2a.5.5 0 1 1 .708.708L2.707 9.5h10.586l-1.147-1.146a.5.5 0 0 1 .708-.708l2 2a.5.5 0 0 1 0 .708l-2 2a.5.5 0 0 1-.708-.708L13.293 10.5H2.707l1.147 1.146a.5.5 0 0 1-.708.708z"/>
  <path d="M5.5 4.5 h5" stroke="currentColor" stroke-width="1" /> 
  <path d="M8 2 v5" stroke="currentColor" stroke-width="1" /> 
</svg>`,
    horizontalminus:`<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-arrows" viewBox="0 0 16 16">
  <path d="M1.146 10.354a.5.5 0 0 1 0-.708l2-2a.5.5 0 1 1 .708.708L2.707 9.5h10.586l-1.147-1.146a.5.5 0 0 1 .708-.708l2 2a.5.5 0 0 1 0 .708l-2 2a.5.5 0 0 1-.708-.708L13.293 10.5H2.707l1.147 1.146a.5.5 0 0 1-.708.708z"/>
  <path d="M5.5 5.5 h5" stroke="currentColor" stroke-width="1" /> 
</svg>`,
    verticalplus:`<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-arrows-vertical" viewBox="0 0 16 16">
        <path d="M8.354 14.854a.5.5 0 0 1-.708 0l-2-2a.5.5 0 0 1 .708-.708L7.5 13.293V2.707L6.354 3.854a.5.5 0 1 1-.708-.708l2-2a.5.5 0 0 1 .708 0l2 2a.5.5 0 0 1-.708.708L8.5 2.707v10.586l1.146-1.147a.5.5 0 0 1 .708.708z"/>
<path d="M10.5 6.5 h5" stroke="currentColor" stroke-width="1" /> 
<path d="M13 4 v5" stroke="currentColor" stroke-width="1" /> 
      </svg>
`,
    verticalminus:`  <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-arrows-vertical" viewBox="0 0 16 16">
        <path d="M8.354 14.854a.5.5 0 0 1-.708 0l-2-2a.5.5 0 0 1 .708-.708L7.5 13.293V2.707L6.354 3.854a.5.5 0 1 1-.708-.708l2-2a.5.5 0 0 1 .708 0l2 2a.5.5 0 0 1-.708.708L8.5 2.707v10.586l1.146-1.147a.5.5 0 0 1 .708.708z"/>
<path d="M10.5 6.5 h5" stroke="currentColor" stroke-width="1" /> 
      </svg>`
    
                      
  };
  
  export default {
    props: {
      iconName: {
        type: String,
        required: true,
      },
      strokeWidth: { type: Number, default: 1 },
      roughness: { type: Number, default: 0 },
      strokeColor: { type: String, default: 'blue' },
      width: {
        type: [Number, String],
        default: 16, // default width in pixels
             },
      height: {
        type: [Number, String],
        default: 16, // default height in pixels
               },
    },
  
    mounted() {
      this.drawIcon();
    },
  
    watch: {
      iconName: 'drawIcon',
    },
  
    methods: {
      drawIcon() {
        const svgMarkup = icons[this.iconName];
        if (!svgMarkup) return;
  
        const svgContainer = document.createElementNS('http://www.w3.org/2000/svg', 'svg');
        svgContainer.setAttribute('width', this.width);
        svgContainer.setAttribute('height', this.height);
        svgContainer.setAttribute('viewBox', '0 0 16 16'); // Change as necessary for the icons
  
        this.$refs.iconContainer.innerHTML = '';
        this.$refs.iconContainer.appendChild(svgContainer);
  
        const rc = rough.svg(svgContainer);
        const paths = new window.DOMParser().parseFromString(svgMarkup, 'image/svg+xml').querySelectorAll('path');
  
        paths.forEach(path => {
          const roughPath = rc.path(path.getAttribute('d'), {
            stroke: this.strokeColor,
            strokeWidth: this.strokeWidth,
            roughness: this.roughness,
          });
          svgContainer.appendChild(roughPath);
        });
      },
    },
  };
  </script>
  
  <style scoped>
  .icon-container {
    position: relative;
  }
  </style>
  