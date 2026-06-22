<template>
  <Transition name="dropdown">
    <div
      v-if="isOpen"
      class="absolute right-0 mt-2 w-72 rounded-2xl bg-white dark:bg-slate-900 border border-slate-200 dark:border-slate-800 shadow-xl p-5 z-50 flex flex-col gap-4 text-left font-sans"
    >
      <h4
        class="text-xs font-bold text-slate-800 dark:text-slate-100 flex items-center gap-1.5 border-b border-slate-100 dark:border-slate-850 pb-2 select-none"
      >
        Configuración de Voz (TTS)
      </h4>

      <!-- Selector de voces -->
      <div class="flex flex-col gap-1.5">
        <label
          class="text-3xs font-extrabold uppercase text-slate-400 dark:text-slate-500 tracking-wider select-none"
          >Voz</label
        >
        <select
          v-model="voiceNameModel"
          class="w-full bg-slate-50 dark:bg-slate-950 border border-slate-200 dark:border-slate-800 rounded-xl px-3 py-2.5 text-xs font-semibold text-slate-700 dark:text-slate-200 outline-none cursor-pointer"
        >
          <option v-if="voices.length === 0" value="">
            Cargando voces...
          </option>
          <option v-for="v in voices" :key="v.name" :value="v.name">
            {{ v.name }}
          </option>
        </select>
      </div>

      <!-- Slider Velocidad -->
      <div class="flex flex-col gap-1.5 select-none">
        <div
          class="flex justify-between items-center text-3xs font-extrabold uppercase text-slate-400 dark:text-slate-500 tracking-wider"
        >
          <span>Velocidad</span>
          <span
            class="font-mono text-primary-650 dark:text-primary-400 text-2xs"
            >{{ voiceRate.toFixed(1) }}x</span
          >
        </div>
        <input
          type="range"
          min="0.5"
          max="2.0"
          step="0.1"
          v-model.number="voiceRateModel"
          class="w-full accent-primary-600 cursor-pointer h-1 bg-slate-100 dark:bg-slate-850 rounded-lg appearance-none"
        />
      </div>

      <!-- Slider Tono -->
      <div class="flex flex-col gap-1.5 select-none">
        <div
          class="flex justify-between items-center text-3xs font-extrabold uppercase text-slate-400 dark:text-slate-500 tracking-wider"
        >
          <span>Tono</span>
          <span
            class="font-mono text-primary-650 dark:text-primary-400 text-2xs"
            >{{ voicePitch.toFixed(1) }}</span
          >
        </div>
        <input
          type="range"
          min="0.5"
          max="2.0"
          step="0.1"
          v-model.number="voicePitchModel"
          class="w-full accent-primary-600 cursor-pointer h-1 bg-slate-100 dark:bg-slate-850 rounded-lg appearance-none"
        />
      </div>

      <!-- Slider Volumen -->
      <div class="flex flex-col gap-1.5 select-none">
        <div
          class="flex justify-between items-center text-3xs font-extrabold uppercase text-slate-400 dark:text-slate-500 tracking-wider"
        >
          <span>Volumen</span>
          <span
            class="font-mono text-primary-650 dark:text-primary-400 text-2xs"
            >{{ Math.round(voiceVolume * 100) }}%</span
          >
        </div>
        <input
          type="range"
          min="0.0"
          max="1.0"
          step="0.1"
          v-model.number="voiceVolumeModel"
          class="w-full accent-primary-600 cursor-pointer h-1 bg-slate-100 dark:bg-slate-850 rounded-lg appearance-none"
        />
      </div>

      <!-- Gemini API Key Setup -->
      <div
        class="border-t border-slate-100 dark:border-slate-850 my-1 pt-3 flex flex-col gap-2.5"
      >
        <!-- Gemini TTS Toggle -->
        <div class="flex items-center justify-between select-none">
          <span
            class="text-3xs font-extrabold uppercase text-slate-400 dark:text-slate-500 tracking-wider"
            >Usar Voz Gemini (IA)</span
          >
          <button
            @click="useGeminiTtsModel = !useGeminiTtsModel"
            class="relative inline-flex h-5 w-9 shrink-0 cursor-pointer rounded-full border-2 border-transparent transition-colors duration-200 ease-in-out focus:outline-none"
            :class="
              useGeminiTts
                ? 'bg-primary-600'
                : 'bg-slate-200 dark:bg-slate-800'
            "
          >
            <span
              class="pointer-events-none inline-block h-4 w-4 transform rounded-full bg-white shadow ring-0 transition duration-200 ease-in-out"
              :class="
                useGeminiTts ? 'translate-x-4' : 'translate-x-0'
              "
            />
          </button>
        </div>

        <div
          class="flex justify-between items-center select-none text-3xs font-extrabold uppercase text-slate-400 dark:text-slate-500 tracking-wider"
        >
          <span>Gemini API Key</span>
        </div>
        <input
          type="password"
          v-model="geminiApiKeyModel"
          placeholder="Almacenada localmente"
          class="w-full bg-slate-50 dark:bg-slate-950 border border-slate-200 dark:border-slate-800 rounded-xl px-3 py-2 text-xs font-semibold text-slate-700 dark:text-slate-200 outline-none"
        />
      </div>

      <!-- Efectos de Sonido -->
      <div
        class="border-t border-slate-100 dark:border-slate-850 my-1 pt-3 flex flex-col gap-1.5"
      >
        <label
          class="text-3xs font-extrabold uppercase text-slate-400 dark:text-slate-500 tracking-wider select-none"
          >Efectos de Sonido (Mecanografía)</label
        >
        <select
          v-model="soundThemeModel"
          class="w-full bg-slate-50 dark:bg-slate-950 border border-slate-200 dark:border-slate-800 rounded-xl px-3 py-2.5 text-xs font-semibold text-slate-700 dark:text-slate-200 outline-none cursor-pointer"
        >
          <option value="none">Sin sonido</option>
          <option value="mechanical">Teclado Mecánico (Click)</option>
          <option value="bubble">Burbuja Digital (Bubble)</option>
          <option value="retro">Retro Game (Beep)</option>
        </select>
      </div>

      <!-- Temas Visuales -->
      <div
        class="border-t border-slate-100 dark:border-slate-850 my-1 pt-3 flex flex-col gap-1.5"
      >
        <label
          class="text-3xs font-extrabold uppercase text-slate-400 dark:text-slate-500 tracking-wider select-none"
          >Tema Visual</label
        >
        <select
          v-model="visualThemeModel"
          class="w-full bg-slate-50 dark:bg-slate-950 border border-slate-200 dark:border-slate-800 rounded-xl px-3 py-2.5 text-xs font-semibold text-slate-700 dark:text-slate-200 outline-none cursor-pointer"
        >
          <option value="default">Estilo Predeterminado</option>
          <option value="cyberpunk">Cyberpunk (Neon Dark)</option>
          <option value="forest">Midnight Forest (Sage)</option>
          <option value="sakura">Sakura Blossom (Cherry)</option>
        </select>
      </div>

      <!-- Modelo de IA (Gemini Model) -->
      <div
        class="border-t border-slate-100 dark:border-slate-850 my-1 pt-3 flex flex-col gap-1.5"
      >
        <label
          class="text-3xs font-extrabold uppercase text-slate-400 dark:text-slate-500 tracking-wider select-none"
          >Modelo de IA (Gemini)</label
        >
        <select
          v-model="aiModelModel"
          class="w-full bg-slate-50 dark:bg-slate-950 border border-slate-200 dark:border-slate-800 rounded-xl px-3 py-2.5 text-xs font-semibold text-slate-700 dark:text-slate-200 outline-none cursor-pointer"
        >
          <option value="gemini-2.5-flash">
            Gemini 2.5 Flash (Alta cuota, recomendado)
          </option>
          <option value="gemini-1.5-flash">
            Gemini 1.5 Flash (Estable, alta cuota)
          </option>
          <option value="gemini-3.5-flash">
            Gemini 3.5 Flash (Experimental, cuota limitada)
          </option>
        </select>
      </div>
    </div>
  </Transition>
</template>

<script setup lang="ts">
import { computed } from "vue";

interface Props {
  isOpen: boolean;
  voices: SpeechSynthesisVoice[];
  selectedVoiceName: string;
  voiceRate: number;
  voicePitch: number;
  voiceVolume: number;
  useGeminiTts: boolean;
  geminiApiKey: string;
  selectedSoundTheme: string;
  selectedTheme: string;
  selectedAiModel: string;
}

const props = defineProps<Props>();

const emit = defineEmits<{
  (e: "update:selectedVoiceName", val: string): void;
  (e: "update:voiceRate", val: number): void;
  (e: "update:voicePitch", val: number): void;
  (e: "update:voiceVolume", val: number): void;
  (e: "update:useGeminiTts", val: boolean): void;
  (e: "update:geminiApiKey", val: string): void;
  (e: "update:selectedSoundTheme", val: string): void;
  (e: "update:selectedTheme", val: string): void;
  (e: "update:selectedAiModel", val: string): void;
}>();

const voiceNameModel = computed({
  get: () => props.selectedVoiceName,
  set: (val) => emit("update:selectedVoiceName", val),
});

const voiceRateModel = computed({
  get: () => props.voiceRate,
  set: (val) => emit("update:voiceRate", val),
});

const voicePitchModel = computed({
  get: () => props.voicePitch,
  set: (val) => emit("update:voicePitch", val),
});

const voiceVolumeModel = computed({
  get: () => props.voiceVolume,
  set: (val) => emit("update:voiceVolume", val),
});

const useGeminiTtsModel = computed({
  get: () => props.useGeminiTts,
  set: (val) => emit("update:useGeminiTts", val),
});

const geminiApiKeyModel = computed({
  get: () => props.geminiApiKey,
  set: (val) => emit("update:geminiApiKey", val),
});

const soundThemeModel = computed({
  get: () => props.selectedSoundTheme,
  set: (val) => emit("update:selectedSoundTheme", val),
});

const visualThemeModel = computed({
  get: () => props.selectedTheme,
  set: (val) => emit("update:selectedTheme", val),
});

const aiModelModel = computed({
  get: () => props.selectedAiModel,
  set: (val) => emit("update:selectedAiModel", val),
});
</script>
