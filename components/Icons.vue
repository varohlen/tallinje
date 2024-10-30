<template>
  <div>
    <component :is="iconName" v-for="(iconName, index) in iconNames" :key="index" />
  </div>
</template>

<script>
export default {
  data() {
    return {
      iconNames: ['PlusIcon', 'MinusIcon', 'RefreshIcon'],
    }
  },
  components: {
    PlusIcon: {
      template: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 -960 960 960"><path d="M784-120 532-372q-30 24-69 38t-83 14q-109 0-184.5-75.5T120-580t75.5-184.5T380-840t184.5 75.5T640-580q0 44-14 83t-38 69l252 252zM380-400q75 0 127.5-52.5T560-580t-52.5-127.5T380-760t-127.5 52.5T200-580t52.5 127.5T380-400m-40-60v-80h-80v-80h80v-80h80v80h80v80h-80v80z"/></svg>` // Ditt SVG-innehåll för plusikonen
    },
    MinusIcon: {
      template: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 -960 960 960"><path d="M784-120 532-372q-30 24-69 38t-83 14q-109 0-184.5-75.5T120-580t75.5-184.5T380-840t184.5 75.5T640-580q0 44-14 83t-38 69l252 252zM380-400q75 0 127.5-52.5T560-580t-52.5-127.5T380-760t-127.5 52.5T200-580t52.5 127.5T380-400M280-540v-80h200v80z"/></svg>` // Ditt SVG-innehåll för minusikonen
    },
    RefreshIcon: {
      template: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 -960 960 960"><path d="M480-160q-134 0-227-93t-93-227 93-227 227-93q69 0 132 28.5T720-690v-110h80v280H520v-80h168q-32-56-87.5-88T480-720q-100 0-170 70t-70 170 70 170 170 70q77 0 139-44t87-116h84q-28 106-114 173t-196 67"/></svg>` // Ditt SVG-innehåll för refreshikonen
    },
  },
}
</script>
