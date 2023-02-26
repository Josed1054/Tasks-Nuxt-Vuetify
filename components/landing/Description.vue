<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="10" md="8" lg="6">
      <CardComponent
        :pfp="imagePFP"
        pfp-size="80"
        title="Welcome to
      Task Tracker 3000."
        text="Task Tracker 3000 is a simple task tracker app
      built with Nuxt.js and Vuetify. Feel free to view, create, edit, and
      delete tasks. 🤠"
        footer="by: Jose Luis Ariza"
      />
      <slot />
    </v-col>
  </v-row>
</template>

<script>
import CardComponent from '~/components/CardComponent.vue';
import imagePFP from '~/static/landing/pfp.png';

export default {
  name: 'DescriptionComponent',
  components: {
    CardComponent,
  },
  data() {
    return {
      imagePFP,
    };
  },
};
</script>
