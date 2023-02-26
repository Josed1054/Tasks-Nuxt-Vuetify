<template>
  <v-table :height="height">
    <thead>
      <tr>
        <template v-for="(header, i) in headers" :key="header">
          <th v-if="i === 0 && sticky" class="stickyStart">
            {{ header }}
          </th>
          <th v-else>
            {{ header }}
          </th>
        </template>
      </tr>
    </thead>
    <tbody>
      <tr v-for="item in items" :key="item.id">
        <template v-for="(value, key, i) in item" :key="value">
          <td
            v-if="key === 'actions'"
            class="d-flex justify-space-between align-center"
            style="min-width: 20rem"
          >
            <template v-for="button in value" :key="button">
              <v-btn :color="button.color" @click="button.action(item.id)">
                {{ button.text }}
              </v-btn>
            </template>
          </td>

          <td v-else-if="key === 'status'">
            <v-btn
              :color="value.done ? 'green' : 'red'"
              @click="value.action(item.id)"
              >{{ value.done ? 'Complete' : 'Incomplete' }}</v-btn
            >
          </td>

          <td v-else-if="i === 0 && sticky" class="stickyStart">
            {{ value }}
          </td>

          <td v-else style="min-width: 10rem">
            {{ value }}
          </td>
        </template>
      </tr>
    </tbody>
  </v-table>
</template>

<script>
export default {
  name: 'TableComponent',
  props: {
    height: {
      type: String,
      default: 'auto',
    },
    headers: {
      type: Array,
      default: () => [],
    },
    items: {
      type: Array,
      default: () => [],
    },
    actions: {
      type: Object,
      default: () => ({}),
    },
    sticky: {
      type: Boolean,
      default: false,
    },
  },
};
</script>

<style>
.stickyStart {
  position: sticky;
  left: 0;
  background-color: white;
  z-index: 1;
}
</style>
