<template>
  <v-avatar icon="mdi-vuetify" :image="image" :size="size" :class="className">
  </v-avatar>
</template>

<script>
export default {
  name: 'ProfilePicture',
  props: {
    image: {
      type: String,
      default: '',
    },
    size: {
      type: String,
      default: '40',
    },
    className: {
      type: String,
      default: '',
    },
  },
};
</script>
