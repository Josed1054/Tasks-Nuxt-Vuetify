<template>
  <v-app-bar class="responsiveNavBar">
    <v-toolbar-title
      class="pr-3"
      style="cursor: pointer; width: min-content"
      @click="$router.push('/')"
      >{{ title }}</v-toolbar-title
    >
    <v-spacer />
    <v-toolbar-items class="hideSmall">
      <v-btn v-for="(item, i) in items" :key="i" text :to="item.to" router>
        {{ item.title }}
      </v-btn>
    </v-toolbar-items>
    <v-btn @click="drawer = !drawer" class="hidden-sm-and-up mr-6">🤖</v-btn>
  </v-app-bar>
  <div
    v-if="xs && drawer"
    class="hidden-sm-and-up d-flex flex-column"
    style="
      position: sticky;
      top: 4rem;
      left: 0;
      width: 100%;
      background-color: white;
      z-index: 999;
      gap: 0.5rem;
      box-shadow: 0px 2px 4px -1px var(--v-shadow-key-umbra-opacity, rgba(0, 0, 0, 0.2)),
        0px 4px 5px 0px
          var(--v-shadow-key-penumbra-opacity, rgba(0, 0, 0, 0.14)),
        0px 1px 10px 0px
          var(--v-shadow-key-penumbra-opacity, rgba(0, 0, 0, 0.12));
    "
  >
    <div
      v-for="(item, i) in items"
      :key="i"
      class="pa-4"
      @click="$router.push(item.to)"
    >
      {{ item.title }}
    </div>
  </div>
</template>

<script>
import { useDisplay } from 'vuetify';

export default {
  name: 'NavBar',
  setup() {
    const { xs } = useDisplay();
    return {
      xs,
    };
  },
  data() {
    return {
      drawer: null,
    };
  },
  props: {
    title: {
      type: String,
      default: 'Task Tracker 3000',
    },
    items: {
      type: Array,
      default: () => [
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
          to: '/create',
        },
      ],
    },
  },
};
</script>

<style>
@media screen and (max-width: 599px) {
  .hideSmall {
    display: none;
  }
}
</style>
