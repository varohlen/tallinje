<template>
  <button 
    class="bg-white hover:bg-gray-100 text-gray-800 py-2 px-2 border border-gray-400 rounded shadow flex items-center justify-center space-x-2"
    @click="$emit('click')"
  >
    <Icons 
      v-if="iconName !== undefined" :iconName="iconName"
      strokeColor="black" 
      width="2rem" 
      height="2rem" 
      :roughness="0.2" 
      :stroke-width="0.5"
    />
    <span v-if="stringName" class="text-base">{{ stringName }}</span>
  </button>
</template>

<script>
import { defineComponent } from 'vue';
import Icons from '@/components/Icons.vue';

export default defineComponent({
  name: 'ControlButton',
  components: { Icons },
  props: {
    iconName: {
      type: String,
      default: undefined,
    },
    stringName: {
      type: String,
      default: ''
    },
  },
  emits: ['click']
});
</script>
