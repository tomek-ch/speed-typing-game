<template>
  <div v-if="state === 'start'">
    <h1>How fast can you type?</h1>
    <button @click="start">Start</button>
  </div>
  <div v-if="state === 'inProgress'">
    <div>Type this:</div>
    <h2>{{ sentence }}</h2>
    <div>Here:</div>
    <input v-model="input" ref="inputRef"/>
    <br>
    <button @click="start">Restart</button>
  </div>
  <div v-else-if="state === 'end'">
    <h2>Your score was:</h2>
    <h1>{{ wordsPerSec }} words per second!</h1>
    <button @click="start">Play again</button>
  </div>
</template>

<script>
import { nextTick, ref, watchEffect } from 'vue';
import sentences from './sentences.json';

export default {
  name: 'App',
  setup() {
    const state = ref('start');
    const input = ref('');
    const sentence = ref(null);
    const inputRef = ref(null);
    const startTime = ref(null);
    const wordsPerSec = ref(null);

    watchEffect(() => {
      if (input.value === sentence.value) {
        state.value = 'end';
        const endTime = Date.now() / 1000;
        const wordCount = sentence.value.split(' ').length;
        wordsPerSec.value = ((wordCount / (endTime - startTime.value))).toFixed(2);
        input.value = '';
      }
    });

    const start = async () => {
      sentence.value = sentences[Math.floor(Math.random() * sentences.length)];
      startTime.value = Date.now() / 1000;
      state.value = 'inProgress';
      nextTick(() => inputRef.value.focus());
    };

    return {
      state,
      sentence,
      input,
      start,
      wordsPerSec,
      inputRef
    };
  },
}
</script>

<style>
body {
  text-align: center;
  font-family: sans-serif;
}
</style>
