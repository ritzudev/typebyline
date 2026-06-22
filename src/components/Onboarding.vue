<template>
  <div
    id="onboarding-container"
    class="flex-grow flex flex-col items-center justify-center p-4 bg-slate-50 dark:bg-slate-950 text-slate-900 dark:text-white transition-colors duration-300 min-h-[calc(100vh-73px)]"
  >
    <!-- Onboarding Step 1: Language selection -->
    <div
      v-if="step === 'lang'"
      id="step-lang"
      class="w-full max-w-xl flex flex-col items-center justify-center text-center animate-fade-in"
    >
      <h2
        class="text-3xl font-extrabold font-display mb-2 tracking-tight text-slate-800 dark:text-white"
      >
        What is your language?
      </h2>
      <p class="text-xs text-slate-400 dark:text-slate-500 mb-8 font-mono">
        Elige tu idioma principal
      </p>

      <div class="grid grid-cols-2 sm:grid-cols-3 gap-4 w-full mb-12">
        <button
          v-for="l in languages"
          :key="l.code"
          @click="selectLanguage(l.code)"
          class="lang-btn p-5 rounded-2xl bg-white dark:bg-slate-900 border-2 border-slate-200/80 dark:border-slate-800/80 hover:border-primary-500/50 dark:hover:border-primary-500/50 hover:bg-slate-50 dark:hover:bg-slate-900/80 shadow-xs hover:shadow-md transition-all flex flex-col items-center gap-3 cursor-pointer group"
        >
          <span class="text-4xl group-hover:scale-110 transition-transform">
            <Flag :lang="l.code" :width="48" :height="32" />
          </span>
          <span class="text-sm font-semibold text-slate-700 dark:text-slate-350">
            {{ l.name }}
          </span>
        </button>
      </div>

      <div class="flex items-center gap-1.5 mt-4">
        <span class="w-8 h-1 bg-primary-500 rounded-full animate-pulse"></span>
        <span class="w-2 h-2 bg-slate-200 dark:bg-slate-800 rounded-full"></span>
        <span class="w-2 h-2 bg-slate-200 dark:bg-slate-800 rounded-full"></span>
      </div>
    </div>

    <!-- Onboarding Step 2: English Level selection -->
    <div
      v-else-if="step === 'level'"
      id="step-level"
      class="w-full max-w-lg flex flex-col justify-center text-left animate-fade-in"
    >
      <button
        @click="goBack"
        class="back-btn mb-6 text-xs text-slate-500 hover:text-slate-700 dark:hover:text-slate-300 flex items-center gap-1.5 cursor-pointer select-none"
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
            d="M15.75 19.5 8.25 12l7.5-7.5"
          />
        </svg>
        <span>{{ t.back }}</span>
      </button>

      <h2
        class="text-3xl font-extrabold font-display mb-2 tracking-tight text-slate-800 dark:text-white"
      >
        {{ t.lvlTitle }}
      </h2>
      <p class="text-sm text-slate-550 dark:text-slate-400 mb-8">
        {{ t.lvlSubtitle }}
      </p>

      <div class="flex flex-col gap-4 w-full mb-12">
        <button
          v-for="lvl in levels"
          :key="lvl.code"
          @click="selectLevel(lvl.code)"
          class="level-btn w-full p-5 rounded-2xl bg-white dark:bg-slate-900 border-2 border-slate-200/80 dark:border-slate-800/80 hover:border-primary-500/50 dark:hover:border-primary-500/50 hover:bg-slate-50 dark:hover:bg-slate-900/80 shadow-xs hover:shadow-md transition-all text-left flex justify-between items-center cursor-pointer group"
        >
          <div class="flex items-center gap-4">
            <span
              :class="[
                'w-3 h-3 rounded-full shadow-md',
                lvl.code === 'beginner' ? 'bg-emerald-500 shadow-emerald-500/20' : '',
                lvl.code === 'intermediate' ? 'bg-sky-500 shadow-sky-500/20' : '',
                lvl.code === 'advanced' ? 'bg-pink-500 shadow-pink-500/20' : ''
              ]"
            ></span>
            <div>
              <p
                class="font-bold text-base text-slate-800 dark:text-white group-hover:text-primary-600 dark:group-hover:text-primary-400 transition-colors"
              >
                {{ t[lvl.titleKey] }}
              </p>
              <p class="text-xs text-slate-400 dark:text-slate-500">
                {{ t[lvl.descKey] }}
              </p>
            </div>
          </div>
          <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="2"
            stroke="currentColor"
            class="w-5 h-5 text-slate-400 group-hover:text-primary-500 group-hover:translate-x-1 transition-all"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M13.5 4.5 21 12m0 0-7.5 7.5M21 12H3"
            />
          </svg>
        </button>
      </div>

      <div class="flex items-center justify-center gap-1.5 w-full mt-4">
        <span class="w-2 h-2 bg-slate-200 dark:bg-slate-800 rounded-full"></span>
        <span class="w-8 h-1 bg-primary-500 rounded-full animate-pulse"></span>
        <span class="w-2 h-2 bg-slate-200 dark:bg-slate-800 rounded-full"></span>
      </div>
    </div>

    <!-- Onboarding Step 3: Nickname input -->
    <div
      v-else-if="step === 'name'"
      id="step-name"
      class="w-full max-w-md flex flex-col justify-center text-left animate-fade-in"
    >
      <button
        @click="goBack"
        class="back-btn mb-6 text-xs text-slate-500 hover:text-slate-700 dark:hover:text-slate-300 flex items-center gap-1.5 cursor-pointer select-none"
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
            d="M15.75 19.5 8.25 12l7.5-7.5"
          />
        </svg>
        <span>{{ t.back }}</span>
      </button>

      <h2
        class="text-3xl font-extrabold font-display mb-2 tracking-tight text-slate-800 dark:text-white"
      >
        {{ t.nameTitle }}
      </h2>
      <p class="text-sm text-slate-550 dark:text-slate-400 mb-8">
        {{ t.nameSubtitle }}
      </p>

      <div class="flex flex-col gap-2 w-full mb-8">
        <div
          id="validation-status"
          :class="[
            'text-xs font-semibold h-5 transition-all',
            isValidated ? 'text-emerald-600 dark:text-emerald-500' : 'text-slate-400 dark:text-slate-500'
          ]"
        >
          <span v-if="isValidating">{{ t.checking }}</span>
          <span v-else-if="isValidated">{{ t.available }}</span>
          <span v-else>&nbsp;</span>
        </div>
        <div class="relative w-full">
          <input
            id="nickname-input"
            type="text"
            ref="inputRef"
            maxlength="15"
            placeholder="cool_reader"
            v-model="nickname"
            @input="handleInput"
            class="w-full p-4 rounded-xl bg-white dark:bg-slate-900 border-2 border-slate-250 dark:border-slate-850 text-slate-800 dark:text-white placeholder-slate-400 dark:placeholder-slate-600 outline-none focus:border-primary-500 transition-all font-semibold"
          />
          <div
            v-if="isValidated && !isValidating"
            id="check-icon"
            class="absolute inset-y-0 right-4 flex items-center"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="3"
              stroke="currentColor"
              class="w-5 h-5 text-emerald-500"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="m4.5 12.75 6 6 9-13.5"
              />
            </svg>
          </div>
          <div
            v-if="isValidating"
            id="loading-spinner"
            class="absolute inset-y-0 right-4 flex items-center"
          >
            <svg
              class="animate-spin h-5 w-5 text-primary-500"
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
        </div>
      </div>

      <button
        id="btn-onboarding-next"
        :disabled="!isValidated || isValidating"
        @click="completeOnboarding"
        class="w-full py-4 bg-primary-600 hover:bg-primary-500 disabled:bg-slate-200 dark:disabled:bg-slate-800 disabled:text-slate-400 dark:disabled:text-slate-650 disabled:cursor-not-allowed text-white font-bold rounded-2xl flex items-center justify-center gap-2 hover:scale-[1.01] active:scale-[0.99] transition-all cursor-pointer shadow-lg shadow-primary-500/10"
      >
        <span>{{ t.continue }}</span>
        <svg
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
          stroke-width="2.5"
          stroke="currentColor"
          class="w-4 h-4"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M13.5 4.5 21 12m0 0-7.5 7.5M21 12H3"
          />
        </svg>
      </button>

      <div class="flex items-center justify-center gap-1.5 w-full mt-10">
        <span class="w-2 h-2 bg-slate-200 dark:bg-slate-800 rounded-full"></span>
        <span class="w-2 h-2 bg-slate-200 dark:bg-slate-800 rounded-full"></span>
        <span class="w-8 h-1 bg-primary-500 rounded-full animate-pulse"></span>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, nextTick, onMounted, onUnmounted } from 'vue';
import Flag from './flags/Flag.vue';

// State definition
const step = ref<'lang' | 'level' | 'name'>('lang');
const nickname = ref('');
const isValidating = ref(false);
const isValidated = ref(false);
const inputRef = ref<HTMLInputElement | null>(null);

const profile = ref({
  language: 'es',
  level: 'beginner',
  nickname: ''
});

// Hardcoded configurations
const languages = [
  { code: 'es', name: 'Español' },
  { code: 'en', name: 'English' }
];

const levels = [
  { code: 'beginner', titleKey: 'lvlBeg', descKey: 'lvlBegDesc' },
  { code: 'intermediate', titleKey: 'lvlInt', descKey: 'lvlIntDesc' },
  { code: 'advanced', titleKey: 'lvlAdv', descKey: 'lvlAdvDesc' }
];

const localization: Record<string, Record<string, string>> = {
  es: {
    back: "Atrás",
    lvlTitle: "¿Tu nivel de inglés?",
    lvlSubtitle: "Empecemos con el contenido adecuado para ti",
    lvlBeg: "Principiante",
    lvlBegDesc: "Empiezo desde cero",
    lvlInt: "Intermedio",
    lvlIntDesc: "Sé algo, quiero avanzar",
    lvlAdv: "Avanzado",
    lvlAdvDesc: "Ya leo bien en inglés",
    nameTitle: "Elige tu apodo",
    nameSubtitle: "Así aparecerá tu nombre en la app",
    checking: "Verificando apodo...",
    available: "Disponible ✓",
    continue: "Continuar",
  },
  en: {
    back: "Back",
    lvlTitle: "Your English level?",
    lvlSubtitle: "Let's start with the right content for you",
    lvlBeg: "Beginner",
    lvlBegDesc: "I start from scratch",
    lvlInt: "Intermediate",
    lvlIntDesc: "I know something, I want to progress",
    lvlAdv: "Advanced",
    lvlAdvDesc: "I already read English well",
    nameTitle: "Choose your nickname",
    nameSubtitle: "How your name will appear in the app",
    checking: "Checking nickname...",
    available: "Available ✓",
    continue: "Continue",
  }
};

// Computed translations
const t = computed(() => {
  const lang = profile.value.language;
  return localization[lang] || localization['es'];
});

// Operations
function selectLanguage(langCode: string) {
  profile.value.language = langCode;
  step.value = 'level';
}

function selectLevel(levelCode: string) {
  profile.value.level = levelCode;
  step.value = 'name';
  nextTick(() => {
    if (inputRef.value) {
      inputRef.value.focus();
    }
  });
}

function goBack() {
  if (step.value === 'level') {
    step.value = 'lang';
  } else if (step.value === 'name') {
    step.value = 'level';
  }
}

let debounceTimer: any = null;
function handleInput() {
  isValidated.value = false;
  isValidating.value = false;
  clearTimeout(debounceTimer);

  const val = nickname.value.trim();
  if (!val) return;

  isValidating.value = true;
  debounceTimer = setTimeout(() => {
    isValidating.value = false;
    isValidated.value = true;
  }, 500);
}

function completeOnboarding() {
  const name = nickname.value.trim();
  if (!name) return;

  profile.value.nickname = name;
  localStorage.setItem('lbl_user_profile', JSON.stringify(profile.value));

  window.dispatchEvent(
    new CustomEvent('onboarding-complete', { detail: profile.value })
  );
}

// Global state reset listener
function handleResetState() {
  nickname.value = '';
  isValidated.value = false;
  isValidating.value = false;
  step.value = 'lang';
}

onMounted(() => {
  window.addEventListener('profile-reset-state', handleResetState);
});

onUnmounted(() => {
  window.removeEventListener('profile-reset-state', handleResetState);
});
</script>

<style scoped>
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(8px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.animate-fade-in {
  animation: fadeIn 0.4s cubic-bezier(0.16, 1, 0.3, 1) forwards;
}
</style>
