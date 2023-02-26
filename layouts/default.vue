<template>
  <v-app>
    <NavBar title="Task Tracker 3000" :items="items" />
    <v-main>
      <v-container>
        <NuxtPage />
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import NavBar from '~/components/NavBar.vue';
export default {
  name: 'DefaultLayout',
  components: {
    NavBar,
  },
  data() {
    return {
      title: 'Task Tracker 3000',
      items: [
        {
          title: 'Home',
          to: '/',
        },
        {
          title: 'View Tasks',
          to: '/tasks',
        },
        {
          title: 'Create Tasks',
          to: '/tasks/create',
        },
      ],
    };
  },
};
</script>
