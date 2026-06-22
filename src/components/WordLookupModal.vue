<template>
  <Transition name="dropdown">
    <div
      v-if="definition"
      id="word-dictionary-card"
      class="absolute bottom-4 z-35 w-[calc(100%-2rem)] left-4 right-4 sm:left-auto sm:right-auto sm:w-full sm:max-w-sm bg-white/95 dark:bg-slate-900/95 border border-slate-200 dark:border-slate-800 shadow-xl rounded-2xl p-4 flex flex-col gap-1.5 backdrop-blur-md text-left select-text relative"
    >
      <!-- Botón de Cerrar (x) -->
      <button
        @click.stop="$emit('close')"
        class="absolute top-3 right-3 text-slate-400 hover:text-slate-600 dark:text-slate-500 dark:hover:text-slate-350 transition-colors cursor-pointer select-none p-1 rounded-lg hover:bg-slate-100 dark:hover:bg-slate-800"
        title="Cerrar Diccionario"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
          stroke-width="2.5"
          stroke="currentColor"
          class="w-3.5 h-3.5"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M6 18 18 6M6 6l12 12"
          />
        </svg>
      </button>

      <div class="flex justify-between items-center pr-6">
        <span
          class="text-xs font-black text-slate-800 dark:text-slate-100 flex items-center gap-1.5 flex-wrap"
        >
          <span
            class="text-xs font-black text-slate-800 dark:text-slate-100"
          >
            {{ definition.word }}
          </span>

          <span
            v-if="definition.phonetic"
            class="text-4xs font-mono text-slate-400 dark:text-slate-500 font-bold"
          >
            {{ definition.phonetic }}
          </span>

          <button
            v-if="definition.audioUrl"
            @click.stop="$emit('play-audio', definition.audioUrl)"
            class="text-slate-400 hover:text-primary-600 dark:text-slate-500 dark:hover:text-primary-400 transition-colors p-1 rounded-md hover:bg-slate-100 dark:hover:bg-slate-800 cursor-pointer"
            title="Escuchar pronunciación"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 20 20"
              fill="currentColor"
              class="w-3 h-3"
            >
              <path
                d="M10 3.75a.75.75 0 0 0-1.264-.546L5.203 6.25H3.5a1 1 0 0 0-1 1v5.5a1 1 0 0 0 1 1h1.703l3.533 3.046A.75.75 0 0 0 10 16.25V3.75ZM13.3 6.7a.75.75 0 1 0-1.1-1.02 6.5 6.5 0 0 1 0 8.64.75.75 0 1 0 1.1-1.02 5 5 0 0 0 0-6.6Z"
              />
              <path
                d="M15.5 8.7a.75.75 0 1 0-1.1-1.02 9.5 9.5 0 0 1 0 4.64.75.75 0 1 0 1.1-1.02 8 8 0 0 0 0-2.6Z"
              />
            </svg>
          </button>

          <span
            v-if="definition.partOfSpeech"
            class="text-4xs font-mono font-bold text-primary-500 uppercase tracking-widest bg-primary-50 dark:bg-primary-950/40 border border-primary-100/30 dark:border-primary-900/20 px-1.5 py-0.5 rounded-md"
          >
            {{ definition.partOfSpeech }}
          </span>
        </span>
        <svg
          v-if="isLoading"
          class="animate-spin h-3.5 w-3.5 text-primary-500"
          fill="none"
          viewBox="0 0 24 24"
        >
          <circle
            class="opacity-25"
            cx="12"
            cy="12"
            r="10"
            stroke="currentColor"
            stroke-width="4"
          ></circle>
          <path
            class="opacity-75"
            fill="currentColor"
            d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
          ></path>
        </svg>
      </div>
      <p
        class="text-2xs text-slate-600 dark:text-slate-350 font-medium"
      >
        {{ definition.definition }}
      </p>
      <p
        v-if="definition.translation"
        class="text-2xs font-bold text-emerald-600 dark:text-emerald-400 flex items-center gap-1"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 20 20"
          fill="currentColor"
          class="w-3.5 h-3.5"
        >
          <path
            d="M3.105 2.289a.75.75 0 0 0-.826.95l1.414 4.925A1.5 1.5 0 0 0 5.135 9.25h6.115a.75.75 0 0 1 0 1.5H5.135a1.5 1.5 0 0 0-1.442 1.086l-1.414 4.926a.75.75 0 0 0 .826.95 28.896 28.896 0 0 0 15.293-9.354.75.75 0 0 0 0-1.022A28.89 28.89 0 0 0 3.105 2.289Z"
          />
        </svg>
        {{ definition.translation }}
      </p>
      <!-- Syllable Pronunciation Guide -->
      <div
        v-if="
          definition.syllables &&
          definition.syllables.length > 0
        "
        class="flex flex-wrap items-center gap-1.5 mt-1 pt-1.5 border-t border-slate-100/60 dark:border-slate-800/30"
      >
        <span
          class="text-4xs font-extrabold uppercase tracking-widest text-slate-400 dark:text-slate-500 mr-0.5"
          >Sílabas</span
        >
        <button
          v-for="(syl, sIdx) in definition.syllables"
          :key="sIdx"
          @click.stop="$emit('speak-syllable', syl)"
          class="px-2 py-0.5 rounded-lg text-3xs font-bold cursor-pointer select-none transition-all border hover:scale-105 active:scale-95"
          :class="
            sIdx === 0
              ? 'bg-primary-50 border-primary-200/60 text-primary-700 dark:bg-primary-950/40 dark:border-primary-800/30 dark:text-primary-400'
              : 'bg-slate-50 border-slate-200/50 text-slate-600 dark:bg-slate-900/50 dark:border-slate-800/30 dark:text-slate-400'
          "
          :title="'Pronunciar: ' + syl"
        >
          {{ syl }}
        </button>
      </div>
    </div>
  </Transition>
</template>

<script setup lang="ts">
interface WordDefinition {
  word: string;
  partOfSpeech: string;
  definition: string;
  translation: string;
  phonetic?: string;
  audioUrl?: string;
  syllables?: string[];
}

defineProps<{
  definition: WordDefinition | null;
  isLoading: boolean;
}>();

defineEmits<{
  (e: 'close'): void;
  (e: 'play-audio', url: string): void;
  (e: 'speak-syllable', text: string): void;
}>();
</script>
