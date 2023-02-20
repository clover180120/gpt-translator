<script setup>
import { reactive, computed } from 'vue';
import {
  createCompletion, newClient, PARTICIPANT_AI, PARTICIPANT_HUMAN,
} from '../api';

defineProps({
  title: {
    type: String,
    default: '',
  },
});

const data = reactive({
  key: '',
  question: '',
  answer: '',
  selectedLang: '英文',
});

const prompt = computed(() => `${PARTICIPANT_HUMAN}: 請將以下內容翻譯成${data.selectedLang}：「${data.question}」。\n${PARTICIPANT_AI}:`);

const translate = async () => {
  const client = newClient(data.key);
  const res = await createCompletion(client)({
    prompt: prompt.value,
    maxTokens: data.key.length * 4,
  });
  const [choice] = res.data.choices;
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
        class="pt-2"
        label="請輸入要翻譯的內容"
        v-model="data.question"
        variant="outlined"
      />
      <v-select
        label="選擇要翻譯成哪種語言"
        :items="['中文', '英文', '日文']"
        v-model="data.selectedLang"
        variant="outlined"
      />
      <v-text-field
        v-model="data.key"
        label="Key"
        type="password"
        variant="outlined"
      />
    </v-card-item>
    <v-card-actions class="d-flex align-center justify-center">
      <v-btn
        variant="outlined"
        @click="translate"
        :disabled="!data.key || !data.question"
      >
        Translate
      </v-btn>
    </v-card-actions>
    <v-card-item>
      <v-textarea
        class="pt-2"
        label="翻譯結果"
        v-model="data.answer"
        variant="outlined"
      />
    </v-card-item>
  </v-card>
</template>

<style scoped>
::v-deep .v-card-item__content {
  overflow: auto !important
}
</style>
