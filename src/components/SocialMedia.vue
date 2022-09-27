<template>
  <button target="_blank" @click="goToLink()" class="bg-violet-500 p-1 md:p-2 text-white inline-flex items-center rounded m-1">
    <IconsSVG :icon="icon"/>
  </button>
</template>

<script>
import IconsSVG from './IconsSVG.vue';

export default {
  name: 'SocialMedia',
  props: {
    icon: String,
    linkURL: String,
  },
  methods: {
    goToLink() {
      window.open(this.linkURL);
    },
  },
  components: { IconsSVG },
}
</script>

