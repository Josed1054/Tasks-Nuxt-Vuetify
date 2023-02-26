<template>
  <v-card :title="title" :subtitle="subtitle" :text="text" :class="className">
    <v-card v-if="pfp" class="text-center py-4">
      <ProfilePictureVue :image="pfp" :alt="alt" :size="pfpSize" />
    </v-card>
    <v-img v-if="image" :src="image" :alt="alt" :class="imageClass" />
    <slot />
    <v-card-text v-if="footer">
      <em
        ><small>{{ footer }}</small></em
      >
    </v-card-text>
    <v-card-actions v-if="buttonText">
      <v-btn @click="onClickButton()">{{ buttonText }}</v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
import ProfilePictureVue from './ProfilePicture.vue';

export default {
  name: 'CardComponent',
  components: {
    ProfilePictureVue,
  },
  props: {
    pfp: {
      type: String,
      default: '',
    },
    pfpSize: {
      type: String,
      default: '40',
    },
    image: {
      type: String,
      default: '',
    },
    alt: {
      type: String,
      default: '',
    },
    imageClass: {
      type: String,
      default: '',
    },
    title: {
      type: String,
      default: '',
    },
    subtitle: {
      type: String,
      default: '',
    },
    text: {
      type: String,
      default: '',
    },
    footer: {
      type: String,
      default: '',
    },
    buttonText: {
      type: String,
      default: '',
    },
    className: {
      type: String,
      default: '',
    },
    onClickButton: {
      type: Function,
      default: () => {},
    },
  },
};
</script>
