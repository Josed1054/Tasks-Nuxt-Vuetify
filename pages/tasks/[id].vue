<script setup>
import CardComponent from '~/components/CardComponent.vue';
import { ref } from 'vue';
const route = useRoute();
const runtimeConfig = useRuntimeConfig();
const id = route.params.id;
const router = useRouter();
const errorDialog = ref(false);
const errorMessage = ref('');
const deleteDialog = ref(false);
const successDeleteDialog = ref(false);

const { data, pending, error, refresh } = await useFetch(
  `https://ecsdevapi.nextline.mx/vdev/tasks-challenge/tasks/${id}`,
  {
    method: 'GET',
    headers: {
      Authorization:
        runtimeConfig.public.apiId + ' ' + runtimeConfig.public.apiKey,
    },
  }
);
const {
  title,
  due_date,
  is_completed,
  description,
  tags,
  token,
  comments,
  created_at,
  updated_at,
} = data.value[0];

let taskCompleted = ref(is_completed);

async function toggleCompleted() {
  console.log(title);
  const formattedTask = {
    title: title,
    is_completed: taskCompleted.value === 1 ? 0 : 1,
    description: description,
    token: token,
    due_date: due_date,
    tags: tags,
    comments: comments,
  };

  const { data: response, error } = await useFetch(
    `https://ecsdevapi.nextline.mx/vdev/tasks-challenge/tasks/${id}`,
    {
      method: 'PUT',
      headers: {
        Authorization:
          runtimeConfig.public.apiId + ' ' + runtimeConfig.public.apiKey,
      },
      body: JSON.stringify(formattedTask),
    }
  );
  if (response) {
    taskCompleted.value = formattedTask.is_completed;
  } else {
    errorDialog.value = true;
    errorMessage.value = error.value;
  }
}

function deleteModal() {
  deleteDialog.value = true;
}

async function deleteTask() {
  deleteDialog.value = false;
  const { data: response, error } = await useFetch(
    `https://ecsdevapi.nextline.mx/vdev/tasks-challenge/tasks/${id}`,
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
    <v-col cols="12" md="8" lg="10" class-name="mt-4">
      <template v-if="pending">
        <CardComponent title="Loading Task Details..." />
      </template>
      <template v-else-if="error">
        <CardComponent title="An error ocurred while loading Task Details..." />
      </template>
      <template v-else>
        <CardComponent :title="title" :subtitle="description">
          <v-col class="d-flex justify-center w-100">
            <v-btn
              :color="taskCompleted === 1 ? 'green' : 'red'"
              class="w-100"
              style="max-width: 32rem"
              @click="toggleCompleted()"
              >{{ taskCompleted === 1 ? 'Complete' : 'Incomplete' }}</v-btn
            >
          </v-col>
          <template class="d-flex align-center flex-column">
            <p>{{ `Due Date: ${due_date || ''}` }}</p>
            <p>{{ `Tags: ${tags || ''}` }}</p>
            <p>{{ `Token: ${token || ''}` }}</p>
            <p>{{ `Comments: ${comments || ''}` }}</p>
            <p>{{ `Created At: ${created_at}` }}</p>
            <p>{{ `Updated At: ${updated_at}` }}</p>
          </template>
          <template
            class="d-flex px-4 w-100 justify-space-around"
            style="gap: 1rem"
          >
            <v-btn
              @click="router.push({ path: `/tasks/edit/${id}` })"
              color="primary"
              class="my-4"
              style="max-width: 32rem; width: 45%"
              >Edit Task</v-btn
            >
            <v-btn
              @click="deleteModal()"
              color="error"
              class="my-4"
              style="max-width: 32rem; width: 45%"
              >Delete Task</v-btn
            >
          </template>
        </CardComponent>
        <v-dialog v-model="errorDialog" persistent>
          <v-card
            class="w-50"
            style="max-width: 1000px; transform: translateX(50%)"
          >
            <v-card-title>Error updating task</v-card-title>
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
              `Are you sure you want to delete the task ${id}?`
            }}</v-card-title>
            <v-card-text>This action cannot be undone.</v-card-text>
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
            <v-card-title>{{ `Task ${id} deleted successfully` }}</v-card-title>
            <v-card-actions>
              <div class="w-100 my-4 d-flex justify-center align-items-center">
                <v-btn color="primary" text @click="$router.push('/tasks')">
                  Go to Tasks
                </v-btn>
              </div>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </template>
    </v-col>
  </v-row>
</template>

<script>
export default {
  name: 'id',
};
</script>
