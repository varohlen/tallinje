<template>
    <div><h1>Detta är min testsida</h1></div>
    <div class="flex justify-center items-center h-screen">
        <p>Här ska min SVG leva</p>
     
      <TestSVG/>
    </div>
  </template>
  
  <script>
  import TestSVG from '~/components/TestSVG.vue';
  import Ellipse from '~/components/Ellipse.vue';
  
  export default {
    components: {
      TestSVG,
      Ellipse
    },
  };
  </script>
     const rc = rough.svg(svg);
     let node = rc.rectangle(10, 10, 200, 200); // x, y, width, height
     svg.appendChild(node);
  <style scoped>
  /* Additional styles if needed */
  </style>
  