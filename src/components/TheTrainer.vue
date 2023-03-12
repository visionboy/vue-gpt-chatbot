<script setup>
import { computed, reactive } from 'vue';
import TheResult from './TheResult.vue';
import {
  createCompletion,
  createClient,
  ROLE_ASSISTANT,
  ROLE_SYSTEM,
  ROLE_USER,
} from '../services/openai';
import {
  DEFAULT_DELAY_SECONDS,
  DEFAULT_SYSTEM_MESSAGE,
  DEFAULT_USER_MESSAGE,
} from '../constants';
import { Message } from '../models';

const data = reactive({
  error: '',
  key: window.atob(localStorage.getItem('key') || ''),
  systemMessage: localStorage.getItem('systemMessage') || DEFAULT_SYSTEM_MESSAGE,
  userMessage: localStorage.getItem('userMessage') || DEFAULT_USER_MESSAGE,
  delaySeconds: Number(localStorage.getItem('delaySeconds')) || DEFAULT_DELAY_SECONDS,
  generatedMessages: [],
  loads: false,
});

const loads = false;

const initMessages = computed(() => [
  new Message(ROLE_SYSTEM, data.systemMessage),
]);

const generatedMessages = computed(() => initMessages.value.concat(data.generatedMessages));

const remember = (key, value) => localStorage.setItem(key, value);

const rememberKey = () => localStorage.setItem('key', window.btoa(data.key));

const run = async () => {
  // data.generatedMessages = [];
  data.loads = true;
  const client = createClient(data.key);
  try {
    const userMessages = data.userMessage.split('\n').filter((userMessage) => !!userMessage);
    for await (const userMessage of userMessages) {
      data.generatedMessages.push(new Message(ROLE_USER, userMessage));
      data.userMessage = '';
      const result = await createCompletion(client)({
        messages: generatedMessages.value,
      });
      const { choices } = result.data;
      const [choice] = choices;
      const { message } = choice;
      data.generatedMessages.push(new Message(ROLE_ASSISTANT, message.content));
      document.getElementById('input-9').focus();
      await new Promise((resolve) => setTimeout(resolve, data.delaySeconds * 1000));
      data.loads = false;
    }
  } catch (err) {
    data.error = err?.response?.data?.error?.message || err.message;
    data.loads = false;
  }
};
</script>

<template>
  <v-snackbar
    v-if="data.error"
    color="indigo-lighten-1"
    model-value
    @update:modelValue="data.error = ''"
  >
    {{ data.error }}
  </v-snackbar>
  <v-row class="justify-center">
    <v-col cols="12" sm="10" md="6">
      <v-card
        color="blue-grey-lighten-5"
        height="100%"
      >
        <v-card-item class="pa-8 pb-4">
          <div class="text-h5 mb-4 font-weight-bold text-indigo">
            Scene
          </div>
          <div class="my-4" style="display: none;">
            <div class="text-subtitle-2 mb-2">
              System Messages
            </div>
            <div>
              <v-textarea
                v-model="data.systemMessage"
                autofocus
                color="indigo"
                hide-details
                no-resize
                rows="4"
                variant="outlined"
              />
            </div>
          </div>
          <div class="my-4">
            <div class="text-subtitle-2 mb-2">
              Delay Seconds
            </div>
            <div>
              <v-text-field
                v-model="data.delaySeconds"
                color="indigo"
                density="compact"
                hide-details
                type="number"
                variant="outlined"
                @input="remember('delaySeconds', data.delaySeconds)"
              />
            </div>
          </div>
          <div class="my-4">
            <div class="text-subtitle-2 mb-2">
              API Key
            </div>
            <div>
              <v-text-field
                v-model="data.key"
                color="indigo"
                density="compact"
                hide-details
                type="password"
                variant="outlined"
                @input="rememberKey"
              />
            </div>
          </div>
        </v-card-item>
      </v-card>
    </v-col>
    <v-col cols="12" sm="10" md="6">
      <v-card
        color="blue-grey-lighten-5"
        height="100%"
      >
        <v-card-item class="pa-8 pb-4">
          <div class="text-h5 mb-4 font-weight-bold text-indigo">
            문의내용
          </div>
          <div class="my-4">
            <TheResult :messages="generatedMessages" />
          </div>
        </v-card-item>
        <v-card-item class="pa-8 pb-4">
          <div class="loading" v-if="data.loads">
            <div class="loader2" />
          </div>
          <div class="my-4">
            <div class="text-subtitle-2 mb-2">
              User Messages
            </div>
            <div>
              <v-textarea
                v-model="data.userMessage"
                color="indigo"
                hide-details
                no-resize
                rows="4"
                variant="outlined"
                @input="remember('userMessage', data.userMessage)"
                ref="msg"
              />
            </div>
          </div>
        </v-card-item>
        <v-card-actions class="justify-end pa-8 pt-0">
          <v-btn
            block
            color="indigo"
            variant="outlined"
            @click="run"
          >
            문의보내기
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
</template>
<style>
  @import "../assets/loading.css";
</style>
