<template>
  <button target="_blank" @click="goToLink()" class="bg-violet-500 m-0.5 md:m-1 px-1 py-0 md:p-2 text-white inline-flex items-center justify-center rounded">
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

