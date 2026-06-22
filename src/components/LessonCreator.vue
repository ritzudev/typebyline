<template>
  <div
    class="w-full max-w-2xl grow flex flex-col justify-center py-6 animate-fade-in text-left font-sans"
  >
    <div
      class="bg-white dark:bg-slate-900 border border-slate-200/60 dark:border-slate-800/40 rounded-3xl p-6 shadow-xl flex flex-col gap-5"
    >
      <div
        class="flex justify-between items-center pb-3 border-b border-slate-100 dark:border-slate-850"
      >
        <h3 class="text-sm font-bold text-slate-800 dark:text-slate-100">
          Crear Nueva Lección
        </h3>
        <div class="flex gap-2">
          <button
            @click="triggerImportFile"
            class="py-1.5 px-3 bg-slate-100 dark:bg-slate-800 text-slate-700 dark:text-slate-200 hover:bg-slate-200 dark:hover:bg-slate-750 text-3xs font-extrabold rounded-xl transition-all cursor-pointer select-none border border-slate-200/50 dark:border-slate-700"
          >
            Importar JSON
          </button>
          <button
            v-if="Object.keys(customLessons).length > 0"
            @click="exportLessons"
            class="py-1.5 px-3 bg-slate-100 dark:bg-slate-800 text-slate-700 dark:text-slate-200 hover:bg-slate-200 dark:hover:bg-slate-750 text-3xs font-extrabold rounded-xl transition-all cursor-pointer select-none border border-slate-200/50 dark:border-slate-700"
          >
            Exportar JSON
          </button>
          <input
            type="file"
            ref="importFileInput"
            @change="importLessons"
            accept=".json"
            class="hidden"
          />
        </div>
      </div>

      <!-- Selector de Modo de Creación -->
      <div
        class="flex border-b border-slate-100 dark:border-slate-850 select-none shrink-0 mb-2"
      >
        <button
          type="button"
          @click="creationTab = 'paste'"
          class="flex-1 pb-3 text-xs font-extrabold uppercase tracking-wider text-center cursor-pointer transition-all border-b-2"
          :class="
            creationTab === 'paste'
              ? 'border-primary-550 text-primary-650 dark:border-primary-500 dark:text-primary-400'
              : 'border-transparent text-slate-400 dark:text-slate-500 hover:text-slate-605 dark:hover:text-slate-400'
          "
        >
          Pegar Texto Propio
        </button>
        <button
          type="button"
          @click="creationTab = 'topic'"
          class="flex-1 pb-3 text-xs font-extrabold uppercase tracking-wider text-center cursor-pointer transition-all border-b-2 flex items-center justify-center gap-1.5"
          :class="
            creationTab === 'topic'
              ? 'border-primary-550 text-primary-650 dark:border-primary-500 dark:text-primary-400'
              : 'border-transparent text-slate-400 dark:text-slate-500 hover:text-slate-650 dark:hover:text-slate-400'
          "
        >
          <span>Generar por Tema (IA)</span>
          <span
            class="px-1.5 py-0.5 rounded-full text-[9px] bg-amber-100 dark:bg-amber-950/40 text-amber-650 dark:text-amber-450 font-bold uppercase tracking-wider"
          >
            Rápido
          </span>
        </button>
      </div>

      <!-- MODO: PEGAR TEXTO -->
      <template v-if="creationTab === 'paste'">
        <div class="flex flex-col gap-1.5">
          <label
            class="text-3xs font-extrabold uppercase text-slate-400 dark:text-slate-500 tracking-wider"
            >Título de la lección / Canción</label
          >
          <input
            type="text"
            v-model="newLessonTitle"
            placeholder="Ej: Yellow - Coldplay o Historia sobre Lina"
            class="w-full bg-slate-50 dark:bg-slate-950 border border-slate-200 dark:border-slate-800 rounded-xl px-4 py-2.5 text-xs font-semibold text-slate-700 dark:text-slate-200 outline-none"
          />
        </div>

        <!-- Selector de Tipo de Contenido -->
        <div class="flex flex-col gap-2">
          <label
            class="text-3xs font-extrabold uppercase text-slate-400 dark:text-slate-500 tracking-wider"
            >Tipo de Contenido</label
          >
          <div class="grid grid-cols-2 sm:grid-cols-4 gap-2">
            <button
              type="button"
              @click="contentType = 'text'"
              class="py-2.5 px-3 rounded-xl border text-xs font-bold transition-all cursor-pointer select-none text-center flex flex-col justify-center items-center gap-1"
              :class="
                contentType === 'text'
                  ? 'bg-primary-50 border-primary-200/60 text-primary-650 dark:bg-primary-950/20 dark:border-primary-900/30 dark:text-primary-400 font-extrabold shadow-sm'
                  : 'bg-slate-50/50 border-slate-200/50 text-slate-600 dark:bg-slate-900/30 dark:border-slate-800/30 dark:text-slate-400 hover:bg-slate-100 dark:hover:bg-slate-900/60'
              "
            >
              <span>Texto / Artículo</span>
            </button>
            <button
              type="button"
              @click="contentType = 'song'"
              class="py-2.5 px-3 rounded-xl border text-xs font-bold transition-all cursor-pointer select-none text-center flex flex-col justify-center items-center gap-1"
              :class="
                contentType === 'song'
                  ? 'bg-primary-50 border-primary-200/60 text-primary-650 dark:bg-primary-950/20 dark:border-primary-900/30 dark:text-primary-400 font-extrabold shadow-sm'
                  : 'bg-slate-50/50 border-slate-200/50 text-slate-600 dark:bg-slate-900/30 dark:border-slate-800/30 dark:text-slate-400 hover:bg-slate-100 dark:hover:bg-slate-900/60'
              "
            >
              <span>Canción (Inglés)</span>
            </button>
            <button
              type="button"
              @click="contentType = 'song_translated'"
              class="py-2.5 px-3 rounded-xl border text-xs font-bold transition-all cursor-pointer select-none text-center flex flex-col justify-center items-center gap-1"
              :class="
                contentType === 'song_translated'
                  ? 'bg-primary-50 border-primary-200/60 text-primary-650 dark:bg-primary-950/20 dark:border-primary-900/30 dark:text-primary-400 font-extrabold shadow-sm'
                  : 'bg-slate-50/50 border-slate-200/50 text-slate-600 dark:bg-slate-900/30 dark:border-slate-800/30 dark:text-slate-400 hover:bg-slate-100 dark:hover:bg-slate-900/60'
              "
            >
              <span>Con Traducción</span>
            </button>
            <button
              type="button"
              @click="contentType = 'dialogue'"
              class="py-2.5 px-3 rounded-xl border text-xs font-bold transition-all cursor-pointer select-none text-center flex flex-col justify-center items-center gap-1"
              :class="
                contentType === 'dialogue'
                  ? 'bg-primary-50 border-primary-200/60 text-primary-650 dark:bg-primary-950/20 dark:border-primary-900/30 dark:text-primary-400 font-extrabold shadow-sm'
                  : 'bg-slate-50/50 border-slate-200/50 text-slate-600 dark:bg-slate-900/30 dark:border-slate-800/30 dark:text-slate-400 hover:bg-slate-100 dark:hover:bg-slate-900/60'
              "
            >
              <span>Diálogo / Entrevista</span>
            </button>
          </div>
        </div>

        <div class="flex flex-col gap-1.5">
          <label
            class="text-3xs font-extrabold uppercase text-slate-400 dark:text-slate-500 tracking-wider"
            >Texto en inglés</label
          >
          <textarea
            v-model="newLessonText"
            rows="6"
            :placeholder="textareaPlaceholder"
            class="w-full bg-slate-50 dark:bg-slate-950 border border-slate-200 dark:border-slate-800 rounded-xl px-4 py-2.5 text-xs font-medium text-slate-700 dark:text-slate-200 outline-none resize-none font-mono transition-all duration-300 focus:border-primary-500/50"
          ></textarea>
        </div>
      </template>

      <!-- MODO: GENERAR POR TEMA (IA EXPRESS) -->
      <template v-else>
        <div class="flex flex-col gap-1.5">
          <label
            class="text-3xs font-extrabold uppercase text-slate-400 dark:text-slate-500 tracking-wider"
          >
            ¿De qué te gustaría hablar o practicar hoy? (Tema)
          </label>
          <input
            type="text"
            v-model="expressTopic"
            placeholder="Ej: Pedir comida, Una entrevista sobre React, Viaje a Londres..."
            class="w-full bg-slate-50 dark:bg-slate-950 border border-slate-200 dark:border-slate-800 rounded-xl px-4 py-2.5 text-xs font-semibold text-slate-700 dark:text-slate-200 outline-none"
          />
        </div>

        <!-- Selector de Tipo de Contenido para Generación -->
        <div class="flex flex-col gap-2">
          <label
            class="text-3xs font-extrabold uppercase text-slate-400 dark:text-slate-500 tracking-wider"
          >
            Formato de la Lección
          </label>
          <div class="grid grid-cols-3 gap-2">
            <button
              type="button"
              @click="contentType = 'text'"
              class="py-2.5 px-3 rounded-xl border text-xs font-bold transition-all cursor-pointer select-none text-center"
              :class="
                contentType === 'text'
                  ? 'bg-primary-50 border-primary-200/60 text-primary-650 dark:bg-primary-950/20 dark:border-primary-900/30 dark:text-primary-400 font-extrabold'
                  : 'bg-slate-50/50 border-slate-200/50 text-slate-650 hover:bg-slate-100 dark:bg-slate-900 dark:border-slate-800'
              "
            >
              <span>Texto / Artículo</span>
            </button>
            <button
              type="button"
              @click="contentType = 'song'"
              class="py-2.5 px-3 rounded-xl border text-xs font-bold transition-all cursor-pointer select-none text-center"
              :class="
                contentType === 'song'
                  ? 'bg-primary-50 border-primary-200/60 text-primary-650 dark:bg-primary-950/20 dark:border-primary-900/30 dark:text-primary-400 font-extrabold'
                  : 'bg-slate-50/50 border-slate-200/50 text-slate-650 hover:bg-slate-100 dark:bg-slate-900 dark:border-slate-800'
              "
            >
              <span>Canción (Líricas)</span>
            </button>
            <button
              type="button"
              @click="contentType = 'dialogue'"
              class="py-2.5 px-3 rounded-xl border text-xs font-bold transition-all cursor-pointer select-none text-center"
              :class="
                contentType === 'dialogue'
                  ? 'bg-primary-50 border-primary-200/60 text-primary-650 dark:bg-primary-950/20 dark:border-primary-900/30 dark:text-primary-400 font-extrabold'
                  : 'bg-slate-50/50 border-slate-200/50 text-slate-650 hover:bg-slate-100 dark:bg-slate-900 dark:border-slate-800'
              "
            >
              <span>Diálogo / Entrevista</span>
            </button>
          </div>
        </div>
      </template>

      <!-- Opciones de optimización -->
      <div
        class="flex flex-col gap-3 py-3.5 px-4 bg-slate-50/50 dark:bg-slate-900/30 border border-slate-200/40 dark:border-slate-800/40 rounded-2xl"
      >
        <p
          class="text-3xs font-extrabold uppercase text-slate-400 dark:text-slate-500 tracking-wider"
        >
          Ajustes de Optimización (Recomendado para canciones)
        </p>

        <div class="flex flex-col gap-3">
          <!-- Switch: Omitir duplicados -->
          <div class="flex items-center justify-between select-none gap-4">
            <div class="flex flex-col">
              <span
                class="text-xs font-bold text-slate-700 dark:text-slate-350"
                >Evitar líneas repetidas</span
              >
              <span class="text-3xs text-slate-400 dark:text-slate-500"
                >Omite líneas idénticas y estribillos duplicados para evitar
                repetir la misma escritura</span
              >
            </div>
            <button
              type="button"
              @click="skipDuplicateLines = !skipDuplicateLines"
              class="relative inline-flex h-5 w-9 shrink-0 cursor-pointer rounded-full border-2 border-transparent transition-colors duration-200 ease-in-out focus:outline-none"
              :class="
                skipDuplicateLines
                  ? 'bg-primary-600'
                  : 'bg-slate-200 dark:bg-slate-800'
              "
            >
              <span
                class="pointer-events-none inline-block h-4 w-4 transform rounded-full bg-white shadow ring-0 transition duration-200 ease-in-out"
                :class="
                  skipDuplicateLines ? 'translate-x-4' : 'translate-x-0'
                "
              />
            </button>
          </div>

          <div
            class="h-px bg-slate-200/50 dark:bg-slate-800/40 w-full"
          ></div>

          <!-- Switch: Limpiar interjecciones -->
          <div class="flex items-center justify-between select-none gap-4">
            <div class="flex flex-col">
              <span
                class="text-xs font-bold text-slate-700 dark:text-slate-350"
                >Limpiar expresiones vacías (Relleno)</span
              >
              <span class="text-3xs text-slate-400 dark:text-slate-500"
                >Elimina expresiones cortas o de fondo como (Hey), (Oh),
                (Yeah) o (Huh)</span
              >
            </div>
            <button
              type="button"
              @click="cleanFillerWords = !cleanFillerWords"
              class="relative inline-flex h-5 w-9 shrink-0 cursor-pointer rounded-full border-2 border-transparent transition-colors duration-200 ease-in-out focus:outline-none"
              :class="
                cleanFillerWords
                  ? 'bg-primary-600'
                  : 'bg-slate-200 dark:bg-slate-800'
              "
            >
              <span
                class="pointer-events-none inline-block h-4 w-4 transform rounded-full bg-white shadow ring-0 transition duration-200 ease-in-out"
                :class="
                  cleanFillerWords ? 'translate-x-4' : 'translate-x-0'
                "
              />
            </button>
          </div>
        </div>
      </div>

      <div
        v-if="errorMessage"
        class="text-2xs text-rose-500 font-semibold bg-rose-50/50 dark:bg-rose-950/20 border border-rose-100/50 dark:border-rose-900/30 p-3 rounded-xl"
      >
        {{ errorMessage }}
      </div>

      <div class="flex flex-col sm:flex-row gap-3 pt-2">
        <!-- Botón Generar con IA (Pegar texto propio) -->
        <button
          v-if="creationTab === 'paste'"
          @click="createLessonWithIA"
          :disabled="
            isGenerating ||
            !newLessonTitle ||
            !newLessonText ||
            !geminiApiKey
          "
          class="flex-1 py-3 px-4 bg-primary-650 hover:bg-primary-700 text-white disabled:bg-slate-200 disabled:text-slate-450 dark:disabled:bg-slate-800/50 dark:disabled:text-slate-650 font-bold text-xs rounded-xl shadow-lg transition-all cursor-pointer disabled:cursor-not-allowed select-none flex items-center justify-center gap-1.5"
        >
          <svg
            v-if="isGenerating"
            class="animate-spin h-3.5 w-3.5 text-white"
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
          <span>{{
            isGenerating
              ? "Procesando con Gemini..."
              : "Generar con IA (Gemini Pro)"
          }}</span>
        </button>

        <!-- Botón Generar por Tema con IA Express -->
        <button
          v-else
          @click="generateLessonExpress"
          :disabled="isGenerating || !expressTopic || !geminiApiKey"
          class="flex-1 py-3 px-4 bg-primary-650 hover:bg-primary-700 text-white disabled:bg-slate-200 disabled:text-slate-450 dark:disabled:bg-slate-800/50 dark:disabled:text-slate-650 font-bold text-xs rounded-xl shadow-lg transition-all cursor-pointer disabled:cursor-not-allowed select-none flex items-center justify-center gap-1.5"
        >
          <svg
            v-if="isGenerating"
            class="animate-spin h-3.5 w-3.5 text-white"
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
          <span>{{
            isGenerating
              ? "Generando Lección Express..."
              : "Generar Lección Express con IA"
          }}</span>
        </button>

        <button
          v-if="creationTab === 'paste'"
          @click="createLessonSimple"
          :disabled="isGenerating || !newLessonTitle || !newLessonText"
          class="flex-1 py-3 px-4 border border-slate-200 dark:border-slate-800 text-slate-700 dark:text-slate-300 hover:bg-slate-50 dark:hover:bg-slate-850 font-bold text-xs rounded-xl transition-all cursor-pointer disabled:cursor-not-allowed select-none"
        >
          Crear lección simple
        </button>
        <button
          @click="$emit('cancel')"
          class="py-3 px-4 text-slate-400 dark:text-slate-500 hover:text-slate-650 dark:hover:text-slate-350 font-bold text-xs rounded-xl transition-all cursor-pointer select-none"
        >
          Cancelar
        </button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch } from "vue";
import { GoogleGenAI } from "@google/genai";

interface PhraseTranslation {
  id: string;
  text: string;
  translations: Record<string, string>;
  keyword?: string;
  keywordTranslations?: string[];
  speakerPrefix?: string;
}

interface Lesson {
  title: Record<string, string>;
  level?: "beginner" | "intermediate" | "advanced";
  phrases: PhraseTranslation[];
}

const props = defineProps<{
  customLessons: Record<string, Lesson>;
  geminiApiKey: string;
  selectedAiModel: string;
}>();

const emit = defineEmits<{
  (e: "lesson-created", payload: { lessonId: string; lesson: Lesson }): void;
  (e: "cancel"): void;
}>();

const creationTab = ref("paste");
const newLessonTitle = ref("");
const newLessonText = ref("");
const contentType = ref("text"); // 'text', 'song', 'song_translated', 'dialogue'
const expressTopic = ref("");
const isGenerating = ref(false);
const errorMessage = ref("");
const importFileInput = ref<HTMLInputElement | null>(null);

const skipDuplicateLines = ref(true);
const cleanFillerWords = ref(true);

// Watcher para activar/desactivar opciones recomendadas según el tipo de contenido
watch(contentType, (newType) => {
  if (newType === "song" || newType === "song_translated") {
    skipDuplicateLines.value = true;
    cleanFillerWords.value = true;
  } else {
    skipDuplicateLines.value = false;
    cleanFillerWords.value = false;
  }
});

const textareaPlaceholder = computed(() => {
  if (contentType.value === "song") {
    return "Pega la letra de tu canción aquí en inglés...";
  } else if (contentType.value === "song_translated") {
    return "Pega la letra alternada:\nLínea en inglés\nLínea traducida al español\nLínea en inglés...";
  } else if (contentType.value === "dialogue") {
    return "Ej:\nInterviewer: Hello, welcome!\nCandidate: Thank you, glad to be here...";
  }
  return "Pega un artículo, párrafo o historia en inglés aquí...";
});

function cleanSongLine(line: string): string {
  let cleaned = line;
  const fillerRegex =
    /\s*\((hey|oh|yeah|huh|ooh|ah|whoa|wow|baby|mmm|eh|vocals|instrumental|vocals\s+only|chuckle|gasp|sigh|huh\?|sí|no|oh-oh|yeah\s+yeah|hey\s+hey|oh\s+oh)\)\s*/gi;
  cleaned = cleaned.replace(fillerRegex, " ").trim();

  const singleFillerRegex =
    /^(hey|oh|yeah|huh|ooh|ah|whoa|wow|mmm|eh|vocals|instrumental|vocals\s+only)$/i;
  const normalizedForCheck = cleaned
    .replace(/[.,\/#!$%\^&\*;:{}=\-_`~()?¿¡]/g, "")
    .trim();
  if (singleFillerRegex.test(normalizedForCheck)) {
    return "";
  }
  cleaned = cleaned.replace(/\s+/g, " ").trim();
  return cleaned;
}

function processAndCleanPhrases(phrases: PhraseTranslation[]): PhraseTranslation[] {
  const seenTexts = new Set<string>();
  const finalPhrases: PhraseTranslation[] = [];

  for (const p of phrases) {
    let englishText = p.text.trim();
    let spanishText = (
      p.translations?.es ||
      p.translations?.en ||
      p.text
    ).trim();

    if (cleanFillerWords.value) {
      englishText = cleanSongLine(englishText);
      spanishText = cleanSongLine(spanishText);
    }

    if (!englishText || englishText.length < 2) {
      continue;
    }

    if (skipDuplicateLines.value) {
      const normText = englishText
        .toLowerCase()
        .replace(/[.,\/#!$%\^&\*;:{}=\-_`~()?¿¡]/g, "")
        .replace(/\s+/g, " ")
        .trim();
      if (seenTexts.has(normText)) {
        continue;
      }
      seenTexts.add(normText);
    }

    const finalTranslations: Record<string, string> = {
      en: englishText,
      es: spanishText || englishText,
    };

    finalPhrases.push({
      ...p,
      text: englishText,
      translations: finalTranslations,
    });
  }
  return finalPhrases;
}

function createLessonSimple() {
  errorMessage.value = "";
  if (!newLessonTitle.value || !newLessonText.value) return;

  const rawLines = newLessonText.value
    .split("\n")
    .map((l) => l.trim())
    .filter((l) => l.length > 0);

  if (rawLines.length === 0) return;

  const lessonId = `custom_${Date.now()}`;
  let finalPhrases: PhraseTranslation[] = [];

  if (contentType.value === "song_translated") {
    // Agrupar de dos en dos (línea impar inglés, línea par español)
    for (let i = 0; i < rawLines.length; i += 2) {
      const eng = rawLines[i];
      const esp = rawLines[i + 1] || eng;
      finalPhrases.push({
        id: `cphrase_${lessonId}_${i}`,
        text: eng,
        translations: {
          en: eng,
          es: esp,
        },
      });
    }
  } else {
    // Formato estándar (una frase por línea)
    finalPhrases = rawLines.map((line, idx) => ({
      id: `cphrase_${lessonId}_${idx}`,
      text: line,
      translations: {
        es: line,
        en: line,
      },
    }));
  }

  finalPhrases = processAndCleanPhrases(finalPhrases);

  const newLesson: Lesson = {
    title: {
      es: newLessonTitle.value,
      en: newLessonTitle.value,
    },
    phrases: finalPhrases,
  };

  emit("lesson-created", { lessonId, lesson: newLesson });

  newLessonTitle.value = "";
  newLessonText.value = "";
  contentType.value = "text";
}

async function createLessonWithIA() {
  errorMessage.value = "";
  if (!newLessonTitle.value || !newLessonText.value) return;
  if (!props.geminiApiKey) {
    errorMessage.value = "Por favor, ingresa tu Gemini API Key en los ajustes de voz.";
    return;
  }

  isGenerating.value = true;

  try {
    let promptText = "";

    if (contentType.value === "dialogue") {
      promptText = `
Eres un asistente experto en traducción y aprendizaje de idiomas. 
Se te proporcionará un diálogo o entrevista en inglés. Tu tarea es traducirlo al español y estructurarlo.

Importante:
1. Identifica y extrae cada línea del diálogo.
2. Mantén el formato de los hablantes al inicio (ej: "Interviewer: ...", "Candidate: ...", "Alex: ..."). Asegúrate de que tanto el texto en inglés como la traducción al español conserven e identifiquen correctamente al hablante correspondiente.
3. Para cada frase en inglés, selecciona una palabra clave (keyword) de vocabulario interesante de la frase y proporciona sus traducciones al español (keywordTranslations).

Genera un objeto JSON estructurado con el formato exacto:
{
  "title": {
    "es": "Traducción al español del título",
    "en": "Título de la entrevista"
  },
  "phrases": [
    {
      "text": "Speaker: frase en inglés",
      "translations": {
        "es": "Speaker: traducción al español"
      },
      "keyword": "palabra clave en inglés",
      "keywordTranslations": ["traducción palabra clave"]
    }
  ]
}

Título de la lección: "${newLessonTitle.value}"
Texto:
"""
${newLessonText.value}
"""

Reglas:
- Conserva el hablante al inicio de las frases (ej: "Interviewer: Hello" -> es: "Interviewer: Hola").
- Retorna ÚNICAMENTE JSON válido. Ningún otro texto adicional.
`;
    } else if (contentType.value === "song") {
      promptText = `
Eres un asistente experto en traducción y aprendizaje de idiomas. 
Se te proporcionará la letra de una canción en inglés. Tu tarea es traducirla al español de forma natural y estructurarla.

Importante:
1. Identifica y extrae cada línea de la canción. Descarta anotaciones entre corchetes o paréntesis como "[Chorus]", "[Verse]", etc.
2. Traduce cada línea con precisión y de forma natural/poética al español.
3. Para cada frase, selecciona una palabra clave y proporciona sus traducciones (keywordTranslations).
4. Omitir líneas duplicadas: Si una línea o frase en inglés ya apareció anteriormente en la canción, no la vuelvas a añadir a la lista de frases para evitar aburrir al usuario escribiendo lo mismo varias veces.
5. Limpiar interjecciones: Elimina palabras vacías e interjecciones sin significado real entre paréntesis o sueltas como "(Hey)", "(Oh)", "(Yeah)", "(Ooh)", "(Baby)", "(Mmm)", etc.

Genera un objeto JSON estructurado con el formato exacto:
{
  "title": {
    "es": "Traducción al español del título",
    "en": "Título de la canción"
  },
  "phrases": [
    {
      "text": "línea en inglés",
      "translations": {
        "es": "traducción en español"
      },
      "keyword": "palabra clave",
      "keywordTranslations": ["traducción palabra clave"]
    }
  ]
}

Título de la lección/canción: "${newLessonTitle.value}"
Texto:
"""
${newLessonText.value}
"""

Reglas:
- Retorna ÚNICAMENTE JSON válido. Ningún otro texto adicional.
`;
    } else if (contentType.value === "song_translated") {
      promptText = `
Eres un asistente experto en traducción de canciones. Se te proporcionará la letra de una canción con líneas alternadas en inglés y español.
Tu tarea:
1. Extrae cada línea en inglés.
2. Asóciala con su traducción correspondiente al español que aparece en la línea siguiente del texto.
3. Extrae una palabra clave interesante para cada frase y sus traducciones al español (keywordTranslations).
4. Omitir líneas duplicadas: Si una línea o frase en inglés ya apareció anteriormente en la canción, no la vuelvas a añadir a la lista de frases para evitar repeticiones tediosas en la lección de escritura.
5. Limpiar interjecciones: Elimina palabras vacías de relleno o coros cortos innecesarios en paréntesis como "(Hey)", "(Oh)", "(Yeah)", "(Ooh)", "(Mmm)", etc., tanto en inglés como en español.

Genera un objeto JSON estructurado con el formato exacto:
{
  "title": {
    "es": "Título en español de la canción",
    "en": "Título en inglés"
  },
  "phrases": [
    {
      "text": "línea en inglés del texto",
      "translations": {
        "es": "línea de traducción en español del texto"
      },
      "keyword": "palabra clave",
      "keywordTranslations": ["traducción palabra clave"]
    }
  ]
}

Título de la lección/canción: "${newLessonTitle.value}"
Texto:
"""
${newLessonText.value}
"""

Reglas:
- Asocia las traducciones que ya vienen en el texto, no inventes traducciones nuevas a menos que falten.
- Retorna ÚNICAMENTE JSON válido. Ningún otro texto adicional.
`;
    } else {
      promptText = `
Eres un asistente experto en traducción y aprendizaje de idiomas. 
Se te proporcionará un texto o artículo en inglés. Tu tarea es dividirlo en oraciones lógicas, traducirlo al español y estructurarlo.

Importante:
1. Extrae cada oración lógica en inglés.
2. Tradúcela con precisión al español.
3. Para cada frase, selecciona una palabra clave y proporciona sus traducciones (keywordTranslations).

Genera un objeto JSON estructurado con el formato exacto:
{
  "title": {
    "es": "Traducción al español del título de la lección",
    "en": "Título de la lección"
  },
  "phrases": [
    {
      "text": "oración en inglés",
      "translations": {
        "es": "traducción en español"
      },
      "keyword": "palabra clave",
      "keywordTranslations": ["traducción palabra clave"]
    }
  ]
}

Título de la lección: "${newLessonTitle.value}"
Texto:
"""
${newLessonText.value}
"""

Reglas:
- Retorna ÚNICAMENTE JSON válido. Ningún otro texto adicional.
`;
    }

    const ai = new GoogleGenAI({ apiKey: props.geminiApiKey });
    const response = await ai.models.generateContent({
      model: props.selectedAiModel,
      contents: promptText,
      config: {
        responseMimeType: "application/json",
      },
    });

    const responseText = response.text;
    if (!responseText) {
      throw new Error("No se obtuvo respuesta de Gemini.");
    }

    const parsedJson = JSON.parse(responseText.trim());
    const lessonId = `custom_${Date.now()}`;

    const rawPhrases = parsedJson.phrases || [];
    let finalPhrases = rawPhrases
      .map((p: any, idx: number) => {
        const textVal = (p.text || "").trim();
        const transVal = (p.translations?.es || p.translation || "").trim();
        return {
          id: `cphrase_${lessonId}_${idx}`,
          text: textVal,
          translations: {
            es: transVal || textVal,
            en: textVal,
          },
          keyword: p.keyword ? p.keyword.trim() : undefined,
          keywordTranslations: p.keywordTranslations || undefined,
        };
      })
      .filter((p: any) => p.text.length > 0);

    finalPhrases = processAndCleanPhrases(finalPhrases);

    if (finalPhrases.length === 0) {
      throw new Error("No se pudieron extraer oraciones en inglés válidas del texto tras la limpieza.");
    }

    const newLesson: Lesson = {
      title: {
        es: parsedJson.title?.es || newLessonTitle.value,
        en: parsedJson.title?.en || newLessonTitle.value,
      },
      phrases: finalPhrases,
    };

    emit("lesson-created", { lessonId, lesson: newLesson });

    newLessonTitle.value = "";
    newLessonText.value = "";
    contentType.value = "text";
  } catch (error: any) {
    console.error(error);
    errorMessage.value = `Error generando lección: ${error.message || error}`;
  } finally {
    isGenerating.value = false;
  }
}

async function generateLessonExpress() {
  errorMessage.value = "";
  if (!expressTopic.value) return;
  if (!props.geminiApiKey) {
    errorMessage.value = "Por favor, ingresa tu Gemini API Key en los ajustes de voz.";
    return;
  }

  isGenerating.value = true;

  try {
    const promptText = `
Eres un asistente experto en enseñanza de inglés y traducción.
El usuario quiere aprender inglés sobre el siguiente tema de interés: "${expressTopic.value}".

Tu tarea es generar una lección de inglés estructurada que contenga exactamente 8 oraciones o frases lógicas cortas de nivel práctico sobre ese tema.
Dependiendo del tipo de contenido seleccionado ("${contentType.value}"), formatea las frases:
- Si es "dialogue", crea un diálogo interactivo entre 2 personas con nombres de hablantes (ej: "A: Hello", "B: Hi").
- Si es "song", crea líneas poéticas o rítmicas de una canción ficticia sobre el tema.
- Para "text" o cualquier otro, crea oraciones narrativas o útiles sobre el tema.

Para cada frase en inglés, proporciona:
1. Su traducción precisa al español.
2. Selecciona una palabra clave (keyword) de vocabulario relevante e interesante de la frase.
3. Proporciona las traducciones al español de esa palabra clave (keywordTranslations).

Genera un objeto JSON estructurado con el formato exacto:
{
  "title": {
    "es": "Título de la lección en español",
    "en": "Título de la lección en inglés"
  },
  "phrases": [
    {
      "text": "Frase u oración en inglés",
      "translations": {
        "es": "Traducción al español de la frase"
      },
      "keyword": "palabra clave en inglés",
      "keywordTranslations": ["traducción palabra clave"]
    }
  ]
}

Reglas:
- Genera exactamente 8 frases.
- No uses abreviaciones ni anotaciones raras.
- Retorna ÚNICAMENTE JSON válido. Ningún otro texto adicional.
`;

    const ai = new GoogleGenAI({ apiKey: props.geminiApiKey });
    const response = await ai.models.generateContent({
      model: props.selectedAiModel,
      contents: promptText,
      config: {
        responseMimeType: "application/json",
      },
    });

    const responseText = response.text;
    if (!responseText) {
      throw new Error("No se obtuvo respuesta de Gemini.");
    }

    const parsedJson = JSON.parse(responseText.trim());
    const lessonId = `custom_${Date.now()}`;

    const rawPhrases = parsedJson.phrases || [];
    let finalPhrases = rawPhrases
      .map((p: any, idx: number) => {
        const textVal = (p.text || "").trim();
        const transVal = (p.translations?.es || p.translation || "").trim();
        return {
          id: `cphrase_${lessonId}_${idx}`,
          text: textVal,
          translations: {
            es: transVal || textVal,
            en: textVal,
          },
          keyword: p.keyword ? p.keyword.trim() : undefined,
          keywordTranslations: p.keywordTranslations || undefined,
        };
      })
      .filter((p: any) => p.text.length > 0);

    finalPhrases = processAndCleanPhrases(finalPhrases);

    if (finalPhrases.length === 0) {
      throw new Error("No se pudieron generar frases válidas tras el filtrado.");
    }

    const newLesson: Lesson = {
      title: {
        es: parsedJson.title?.es || expressTopic.value,
        en: parsedJson.title?.en || expressTopic.value,
      },
      phrases: finalPhrases,
    };

    emit("lesson-created", { lessonId, lesson: newLesson });

    expressTopic.value = "";
  } catch (error: any) {
    console.error(error);
    errorMessage.value = `Error generando lección rápida: ${error.message || error}`;
  } finally {
    isGenerating.value = false;
  }
}

function exportLessons() {
  const dataStr =
    "data:text/json;charset=utf-8," +
    encodeURIComponent(JSON.stringify(props.customLessons, null, 2));
  const downloadAnchor = document.createElement("a");
  downloadAnchor.setAttribute("href", dataStr);
  downloadAnchor.setAttribute("download", `lessons_backup_${Date.now()}.json`);
  document.body.appendChild(downloadAnchor);
  downloadAnchor.click();
  downloadAnchor.remove();
}

function triggerImportFile() {
  if (importFileInput.value) {
    importFileInput.value.click();
  }
}

function importLessons(e: Event) {
  errorMessage.value = "";
  const target = e.target as HTMLInputElement;
  if (!target.files || target.files.length === 0) return;

  const file = target.files[0];
  const reader = new FileReader();
  reader.onload = (event) => {
    try {
      const parsed = JSON.parse(event.target?.result as string);
      if (typeof parsed !== "object" || parsed === null) {
        throw new Error("Formato de JSON inválido.");
      }

      // Emitimos cada lección cargada
      for (const [key, val] of Object.entries(parsed)) {
        emit("lesson-created", { lessonId: key, lesson: val as Lesson });
      }
      target.value = "";
    } catch (err: any) {
      errorMessage.value = `Error al importar: ${err.message || err}`;
    }
  };
  reader.readAsText(file);
}
</script>
