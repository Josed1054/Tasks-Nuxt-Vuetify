<script setup>
import CardComponent from '~~/components/CardComponent.vue';
import { ref } from 'vue';
const router = useRouter();

const runtimeConfig = useRuntimeConfig();

const title = ref('');
const is_completed = ref(false);
const description = ref('');
const token = ref('');
const due_date = ref('');
const tags = ref(null);
const comment = ref('');
const titleRules = [(v) => !!v || 'Title is required'];
const tagItems = ['Work', 'Personal', 'Home', 'School'];
const valid = ref(false);

const successDialog = ref(false);
const errorDialog = ref(false);
const errorMessage = ref('');

async function submitForm() {
  if (valid.value) {
    // Submit form
    const data = {
      title: title.value,
      is_completed: is_completed.value ? 1 : 0,
      description: description.value,
      due_date: due_date.value,
      tags: tags.value,
      comments: comment.value,
    };

    const { data: response, error } = await useFetch(
      'https://ecsdevapi.nextline.mx/vdev/tasks-challenge/tasks',
      {
        method: 'POST',
        headers: {
          Authorization:
            runtimeConfig.public.apiId + ' ' + runtimeConfig.public.apiKey,
        },
        body: JSON.stringify(data), // Convert data to JSON string
      }
    );

    if (response) {
      // Handle successful response
      console.log('Task created successfully:', response);
      successDialog.value = true;
    } else {
      // Handle error
      console.error('Failed to create task:', error);
      errorMessage.value = error.message;
      errorDialog.value = true;
    }
  }
}
function navigateToTasks() {
  // Navigate to /tasks route
  router.push({ path: '/tasks' });
}
</script>

<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="10" md="8" lg="6">
      <CardComponent title="Create a new task.">
        <v-form v-model="valid">
          <v-text-field
            v-model="title"
            label="Title"
            :rules="titleRules"
            required
          ></v-text-field>
          <v-checkbox
            v-model="is_completed"
            :label="is_completed ? 'Complete' : 'Incomplete'"
          ></v-checkbox>
          <v-textarea v-model="description" label="Description"></v-textarea>
          <v-text-field v-model="token" label="Token"></v-text-field>
          <v-text-field
            v-model="due_date"
            label="Due Date"
            type="date"
          ></v-text-field>
          <v-text-field v-model="comment" label="Comment"></v-text-field>
          <v-select
            v-model="tags"
            label="Tags"
            :items="tagItems"
            chips
          ></v-select>
          <div class="w-100 my-4 d-flex justify-center align-items-center">
            <v-btn
              :disabled="!valid"
              color="primary"
              class="w-100"
              style="max-width: 32rem"
              @click="submitForm"
            >
              Create Task
            </v-btn>
          </div>
        </v-form>
      </CardComponent>
      <v-dialog v-model="successDialog" persistent>
        <v-card
          class="w-50"
          style="max-width: 1000px; transform: translateX(50%)"
        >
          <v-card-title>Task created successfully!</v-card-title>
          <v-card-actions>
            <div class="w-100 my-4 d-flex justify-center align-items-center">
              <v-btn color="primary" text @click="navigateToTasks">
                Go to tasks
              </v-btn>
            </div>
          </v-card-actions>
        </v-card>
      </v-dialog>
      <v-dialog v-model="errorDialog" persistent>
        <v-card
          class="w-50"
          style="max-width: 1000px; transform: translateX(50%)"
        >
          <v-card-title>Error creating task</v-card-title>
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
    </v-col>
  </v-row>
</template>
