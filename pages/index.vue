<template>
  <div class="container flex flex-col mx-auto">
    <h2 class="font-semibold m-2 text-lg">
      Text To Speech / Speech Synthesis Utterance
    </h2>
    <div class="mb-2 relative">
      <label class="absolute ml-3 mt-2 text-orange-400 text-sm" for="text"
        >Choose a voice</label
      >
      <select
        v-model="voice"
        class="bg-gray-200 border border-gray-800 p-2 pt-8 rounded w-full"
        name="voice"
      >
        <option
          v-for="voice in voices"
          :key="`${voice.name}-${voice.lang}`"
          :value="voice.name"
          >{{ voice.name }} ({{ voice.lang }})</option
        >
      </select>
    </div>
    <div class="mb-2 relative">
      <label class="absolute ml-3 mt-2 text-orange-400 text-sm" for="text"
        >Enter text to speak</label
      >
      <textarea
        v-model="text"
        class="bg-gray-200 border border-gray-800 flex pb-2 pt-8 px-3 resize-none rounded w-full"
        cols="100"
        name="text"
        rows="5"
      ></textarea>
    </div>
    <div class="flex items-center justify-between mb-2">
      <div class="flex-grow relative">
        <label class="absolute ml-3 mt-2 text-orange-400 text-sm" for="pitch"
          >Alter speech pitch</label
        >
        <input
          v-model="pitch"
          class="bg-gray-200 border border-gray-800 flex pb-2 pt-8 px-3 rounded w-full"
          max="10"
          min="0"
          name="pitch"
          step="0.1"
          type="number"
        />
      </div>
      <span class="w-2" />
      <div class="flex-grow relative">
        <label class="absolute ml-3 mt-2 text-orange-400 text-sm" for="rate"
          >Alter speech rate</label
        >
        <input
          v-model="rate"
          class="bg-gray-200 border border-gray-800 flex pb-2 pt-8 px-3 rounded w-full"
          max="10"
          min="0"
          name="rate"
          step="0.1"
          type="number"
        />
      </div>
      <span class="w-2" />
      <div class="flex-grow relative">
        <label class="absolute ml-3 mt-2 text-orange-400 text-sm" for="volume"
          >Alter speech volume</label
        >
        <input
          v-model="volume"
          class="bg-gray-200 border border-gray-800 flex pb-2 pt-8 px-3 rounded w-full"
          max="10"
          min="0"
          name="volume"
          step="0.1"
          type="number"
        />
      </div>
    </div>
    <div class="flex items-center justify-end mb-2">
      <p class="text-sm">
        {{ speaking ? 'Currently speaking' : 'Not currently speaking' }}
      </p>
    </div>
    <div class="flex items-center justify-between mb-2">
      <button
        class="bg-gray-800 flex-grow px-4 py-2 rounded text-white"
        :class="{ 'cursor-not-allowed opacity-50 ': !voice || !text }"
        :disabled="!voice || !text"
        @click="speak"
      >
        Speak
      </button>
      <span class="w-2" />
      <button
        class="bg-gray-800 flex-grow px-4 py-2 rounded text-white"
        :class="{ 'cursor-not-allowed opacity-50 ': !voice || !text }"
        :disabled="!voice || !text"
        @click="pause"
      >
        Pause
      </button>
      <span class="w-2" />
      <button
        class="bg-gray-800 flex-grow px-4 py-2 rounded text-white"
        :class="{ 'cursor-not-allowed opacity-50 ': !voice || !text }"
        :disabled="!voice || !text"
        @click="resume"
      >
        Resume
      </button>
      <span class="w-2" />
      <button
        class="bg-gray-800 flex-grow px-4 py-2 rounded text-white"
        :class="{ 'cursor-not-allowed opacity-50 ': !voice || !text }"
        :disabled="!voice || !text"
        @click="cancel"
      >
        Cancel
      </button>
    </div>
  </div>
</template>

<script>
export default {
  data: () => ({
    log: null,
    pitch: 1,
    rate: 1,
    speaking: false,
    synth: null,
    text: null,
    voice: null,
    voices: null,
    volume: 1
  }),
  mounted() {
    this.$nextTick(() => {
      this.synth = window.speechSynthesis
      this.voices = this.synth.getVoices()
      if (this.synth === null && this.voices === null) {
        /* eslint-disable-next-line */
        console.error('Speech Synthesis not found')
      }
    })
  },
  methods: {
    speak() {
      const selectedVoice = this.voices.find((voice) => voice === this.voice)

      const utterance = new SpeechSynthesisUtterance(this.text)

      utterance.addEventListener('boundary', (event) => this.eventLog(event))
      utterance.addEventListener('end', (event) => this.eventLog(event))
      utterance.addEventListener('error', (event) => this.eventLog(event))
      utterance.addEventListener('mark', (event) => this.eventLog(event))
      utterance.addEventListener('pause', (event) => this.eventLog(event))
      utterance.addEventListener('resume', (event) => this.eventLog(event))
      utterance.addEventListener('start', (event) => this.eventLog(event))
      utterance.voice = selectedVoice

      this.synth.speak(utterance)
    },
    pause() {
      this.synth.pause()
    },
    resume() {
      this.synth.resume()
    },
    cancel() {
      this.synth.cancel()
    },
    eventLog(event) {
      const { type } = event
      if (type === 'start') this.speaking = true
      if (type === 'end') this.speaking = false
    }
  }
}
</script>
