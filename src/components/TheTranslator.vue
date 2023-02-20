<script setup>
import { ref, reactive, computed } from 'vue';
import {
  createCompletion, newClient, PARTICIPANT_HUMAN, PARTICIPANT_AI,
} from '../api';

defineProps({
  title: {
    type: String,
    default: '',
  },
});

const key = ref('');

const data = reactive({
  question: '',
  answer: '',
});

const prompt = computed(() => `${PARTICIPANT_HUMAN}: 請將以下內容翻譯成英文：「${data.question}」。\n${PARTICIPANT_AI}:`);

const translate = async () => {
  const client = newClient(key.value);
  const res = await createCompletion(client)({
    prompt: prompt.value,
    maxTokens: data.question.length * 4,
  });
  const { choices } = res.data;
  const [choice] = choices;
  const { text } = choice;
  data.answer = text.trim();
};
</script>

<template>
  <v-card>
    <v-card-title>
      {{ title }}
    </v-card-title>
    <v-card-item>
      <v-textarea
        v-model="data.question"
        variant="outlined"
      />
      <v-textarea
        v-model="data.answer"
        variant="outlined"
      />
      <v-text-field
        v-model="key"
        label="Key"
        type="password"
        variant="outlined"
      />
    </v-card-item>
    <v-card-actions class="text-center">
      <v-spacer />
      <v-btn
        variant="outlined"
        @click="translate"
      >
        Translate
      </v-btn>
    </v-card-actions>
  </v-card>
</template>
