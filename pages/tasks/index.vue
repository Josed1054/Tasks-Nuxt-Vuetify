<script setup>
import { ref } from 'vue';
import CardComponent from '~/components/CardComponent.vue';
import TableComponent from '~/components/tasks/TableComponent.vue';

const items = ref([]);
const runtimeConfig = useRuntimeConfig();
const headers = ['ID', 'Title', 'Due Date', 'Status', 'Actions'];
const router = useRouter();
const errorDialog = ref(false);
const errorId = ref('');
const errorMessage = ref('');
const deleteDialog = ref(false);
const deleteId = ref();
const successDeleteDialog = ref(false);

const { data, pending, error, refresh } = await useFetch(
  'https://ecsdevapi.nextline.mx/vdev/tasks-challenge/tasks',
  {
    method: 'GET',
    headers: {
      Authorization:
        runtimeConfig.public.apiId + ' ' + runtimeConfig.public.apiKey,
    },
  }
);

items.value = data.value.map((element) => {
  return {
    id: element.id,
    title: element.title,
    due_date: element.due_date,
    status: {
      done: element.is_completed,
      action: (id) => {
        toggleCompleted(id);
      },
    },
    actions: [
      {
        text: 'View',
        color: 'blue',
        action: (id) => {
          router.push({ path: `/tasks/${id}` });
        },
      },
      {
        text: 'Edit',
        color: 'primary',
        action: (id) => {
          router.push({ path: `/tasks/edit/${id}` });
        },
      },
      {
        text: 'Delete',
        color: 'error',
        action: (id) => {
          deleteModal(id);
        },
      },
    ],
  };
});

async function toggleCompleted(id) {
  const task = items.value.find((item) => item.id === id);
  const formattedTask = {
    title: task.title,
    is_completed: task.status.done ? 0 : 1,
    due_date: task.due_date,
  };
  const { data: response, error } = await useFetch(
    `https://ecsdevapi.nextline.mx/vdev/tasks-challenge/tasks/${id}`,
    {
      method: 'PUT',
      headers: {
        Authorization:
          runtimeConfig.public.apiId + ' ' + runtimeConfig.public.apiKey,
      },
      body: JSON.stringify(formattedTask), // Convert data to JSON string
    }
  );
  if (response) {
    items.value = items.value.map((item) => {
      if (item.id === id) {
        item.status.done = item.status.done ? false : true;
      }
      return item;
    });
  } else {
    errorDialog.value = true;
    errorId.value = id;
    errorMessage.value = error.value;
  }
}

function deleteModal(id) {
  deleteDialog.value = true;
  deleteId.value = id;
}

async function deleteTask() {
  deleteDialog.value = false;
  const { data: response, error } = await useFetch(
    `https://ecsdevapi.nextline.mx/vdev/tasks-challenge/tasks/${deleteId}`,
    {
      method: 'DELETE',
      headers: {
        Authorization:
          runtimeConfig.public.apiId + ' ' + runtimeConfig.public.apiKey,
      },
    }
  );
  if (response) {
    successDeleteDialog.value = true;
  } else {
    errorDialog.value = true;
    errorMessage.value = error.value;
  }
}
</script>

<template>
  <v-row justify="center" align="center">
    <v-col cols="12">
      <CardComponent
        title="List of tasks."
        subtitle="View, Create, Edit & Delete Tasks."
      >
        <v-col class="d-flex justify-center w-100">
          <v-btn
            @click="this.$router.push(`/tasks/create`)"
            color="primary"
            class="w-100 my-4"
            style="max-width: 32rem"
            >Create Task</v-btn
          >
        </v-col>
        <TableComponent
          height="500px"
          :headers="headers"
          :items="items"
          :sticky="true"
        />
      </CardComponent>
      <v-dialog v-model="errorDialog" persistent>
        <v-card
          class="w-50"
          style="max-width: 1000px; transform: translateX(50%)"
        >
          <v-card-title>{{ `Error updating task ${errorId}` }}</v-card-title>
          <v-card-text>{{ errorMessage }}</v-card-text>
          <v-card-actions>
            <div class="w-100 my-4 d-flex justify-center align-items-center">
              <v-btn color="primary" text @click="errorDialog = false">
                Retry
              </v-btn>
            </div>
          </v-card-actions>
        </v-card>
      </v-dialog>
      <v-dialog v-model="deleteDialog">
        <v-card
          class="w-50"
          style="max-width: 1000px; transform: translateX(50%)"
        >
          <v-card-title>{{
            `Are you sure you want to delete the task ${deleteId}?`
          }}</v-card-title>
          <v-card-text>This action cannot be reversed.</v-card-text>
          <v-card-actions>
            <div
              class="w-100 my-4 d-flex justify-space-around align-items-center"
            >
              <v-btn color="primary" text @click="deleteDialog = false">
                Cancel
              </v-btn>
              <v-btn color="error" text @click="deleteTask"> Delete </v-btn>
            </div>
          </v-card-actions>
        </v-card>
      </v-dialog>
      <v-dialog v-model="successDeleteDialog" persistent>
        <v-card
          class="w-50"
          style="max-width: 1000px; transform: translateX(50%)"
        >
          <v-card-title>{{
            `Task ${deleteId} deleted successfully`
          }}</v-card-title>
          <v-card-actions>
            <div class="w-100 my-4 d-flex justify-center align-items-center">
              <v-btn color="primary" text @click="successDeleteDialog = false">
                Ok
              </v-btn>
            </div>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-col>
  </v-row>
</template>

<script>
export default {
  name: 'DescriptionComponent',
};
</script>
