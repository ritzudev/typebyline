<template>
  <div>
    <!-- BACKDROP FOR MOBILE SIDEBAR -->
    <div
      v-if="showSidebar"
      class="fixed inset-0 bg-slate-900/40 dark:bg-slate-950/60 backdrop-blur-xs z-45 md:hidden"
      @click="$emit('toggle-sidebar')"
    ></div>

    <!-- LEFT SIDEBAR (COMPLETED LIST) -->
    <Transition name="sidebar">
      <aside
        v-if="showSidebar"
        class="w-full md:w-80 border-r border-slate-200/60 dark:border-slate-800/40 bg-slate-50/50 dark:bg-slate-950/20 p-5 flex flex-col justify-start shrink-0 relative overflow-y-auto max-h-50 md:max-h-[95vh] z-46"
      >
        <!-- Tabs selector -->
        <div
          class="flex border-b border-slate-200/60 dark:border-slate-800/40 mb-4 select-none shrink-0"
        >
          <button
            @click="activeSidebarTab = 'roadmap'"
            class="flex-1 pb-3 text-[11px] font-extrabold uppercase tracking-wider text-center cursor-pointer transition-all border-b-2"
            :class="
              activeSidebarTab === 'roadmap'
                ? 'border-primary-550 text-primary-650 dark:border-primary-500 dark:text-primary-400'
                : 'border-transparent text-slate-400 dark:text-slate-500 hover:text-slate-650 dark:hover:text-slate-400'
            "
          >
            Ruta
          </button>
          <button
            @click="activeSidebarTab = 'custom'"
            class="flex-1 pb-3 text-[11px] font-extrabold uppercase tracking-wider text-center cursor-pointer transition-all border-b-2"
            :class="
              activeSidebarTab === 'custom'
                ? 'border-primary-550 text-primary-650 dark:border-primary-500 dark:text-primary-400'
                : 'border-transparent text-slate-400 dark:text-slate-500 hover:text-slate-650 dark:hover:text-slate-400'
            "
          >
            Mis Lecciones
          </button>
        </div>

        <!-- RUTA DE APRENDIZAJE TAB -->
        <div
          v-if="activeSidebarTab === 'roadmap'"
          class="flex flex-col gap-4 overflow-y-auto pr-1 grow select-none"
        >
          <!-- Principiante (A1) -->
          <div class="flex flex-col gap-1.5" v-if="lessonsByLevel.beginner.length > 0">
            <span class="text-[9px] font-black uppercase tracking-widest text-slate-400 dark:text-slate-500 mb-1 flex items-center gap-1.5">
              <span>🟢</span> Principiante (A1)
            </span>
            <button
              v-for="lesson in lessonsByLevel.beginner"
              :key="lesson.id"
              @click="$emit('select-lesson', lesson.id)"
              class="w-full text-left px-3.5 py-2.5 rounded-xl border text-xs font-bold transition-all flex items-center justify-between cursor-pointer"
              :class="
                lesson.id === activeLessonId
                  ? 'bg-primary-50/70 border-primary-200 text-primary-650 dark:bg-primary-900 dark:border-primary-900/40 dark:text-primary-400'
                  : 'bg-white border-slate-200/60 text-slate-700 dark:bg-slate-900 dark:border-slate-800/60 dark:text-slate-300 hover:bg-slate-50 dark:hover:bg-slate-700'
              "
            >
              <span class="truncate pr-1 flex items-center gap-1.5">
                <span v-if="completedLessons[lesson.id]" class="text-emerald-500 shrink-0">✅</span>
                <span>{{ lesson.title[profile.language] || lesson.title["es"] }}</span>
              </span>
            </button>
          </div>

          <!-- Intermedio (B1) -->
          <div class="flex flex-col gap-1.5" v-if="lessonsByLevel.intermediate.length > 0">
            <span class="text-[9px] font-black uppercase tracking-widest text-slate-400 dark:text-slate-500 mb-1 flex items-center gap-1.5">
              <span>🟡</span> Intermedio (B1)
            </span>
            <button
              v-for="lesson in lessonsByLevel.intermediate"
              :key="lesson.id"
              @click="$emit('select-lesson', lesson.id)"
              class="w-full text-left px-3.5 py-2.5 rounded-xl border text-xs font-bold transition-all flex items-center justify-between cursor-pointer"
              :class="
                lesson.id === activeLessonId
                  ? 'bg-primary-50/70 border-primary-200 text-primary-650 dark:bg-primary-900 dark:border-primary-900/40 dark:text-primary-400'
                  : 'bg-white border-slate-200/60 text-slate-700 dark:bg-slate-900 dark:border-slate-800/60 dark:text-slate-300 hover:bg-slate-50 dark:hover:bg-slate-700'
              "
            >
              <span class="truncate pr-1 flex items-center gap-1.5">
                <span v-if="completedLessons[lesson.id]" class="text-emerald-500 shrink-0">✅</span>
                <span>{{ lesson.title[profile.language] || lesson.title["es"] }}</span>
              </span>
            </button>
          </div>

          <!-- Avanzado (C1) -->
          <div class="flex flex-col gap-1.5" v-if="lessonsByLevel.advanced.length > 0">
            <span class="text-[9px] font-black uppercase tracking-widest text-slate-400 dark:text-slate-500 mb-1 flex items-center gap-1.5">
              <span>🔴</span> Avanzado (C1)
            </span>
            <button
              v-for="lesson in lessonsByLevel.advanced"
              :key="lesson.id"
              @click="$emit('select-lesson', lesson.id)"
              class="w-full text-left px-3.5 py-2.5 rounded-xl border text-xs font-bold transition-all flex items-center justify-between cursor-pointer"
              :class="
                lesson.id === activeLessonId
                  ? 'bg-primary-50/70 border-primary-200 text-primary-650 dark:bg-primary-900 dark:border-primary-900/40 dark:text-primary-400'
                  : 'bg-white border-slate-200/60 text-slate-700 dark:bg-slate-900 dark:border-slate-800/60 dark:text-slate-300 hover:bg-slate-50 dark:hover:bg-slate-700'
              "
            >
              <span class="truncate pr-1 flex items-center gap-1.5">
                <span v-if="completedLessons[lesson.id]" class="text-emerald-500 shrink-0">✅</span>
                <span>{{ lesson.title[profile.language] || lesson.title["es"] }}</span>
              </span>
            </button>
          </div>
        </div>

        <!-- MIS LECCIONES TAB -->
        <div
          v-if="activeSidebarTab === 'custom'"
          class="flex flex-col gap-4 overflow-y-auto pr-1 grow select-none"
        >
          <!-- Botón Crear Nueva Lección -->
          <button
            @click="$emit('select-lesson', 'create_new_lesson')"
            class="w-full py-2.5 px-4 bg-primary-650 hover:bg-primary-700 text-white rounded-xl font-bold text-xs shadow-lg shadow-primary-500/10 transition-all cursor-pointer text-center flex items-center justify-center gap-1.5 select-none shrink-0"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="3"
              stroke="currentColor"
              class="w-4 h-4"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M12 4.5v15m7.5-7.5h-15"
              />
            </svg>
            <span>Crear Nueva Lección</span>
          </button>

          <div class="h-px bg-slate-200/50 dark:bg-slate-800/40 my-1 shrink-0"></div>

          <!-- Creadas por el usuario -->
          <div class="flex flex-col gap-1.5" v-if="Object.keys(customLessons).length > 0">
            <span class="text-[9px] font-black uppercase tracking-widest text-slate-400 dark:text-slate-500 mb-1">
              Tus Temas Creados
            </span>
            <div
              v-for="(lesson, key) in customLessons"
              :key="key"
              class="relative group"
            >
              <button
                @click="$emit('select-lesson', key)"
                class="w-full text-left pl-3.5 pr-9 py-2.5 rounded-xl border text-xs font-bold transition-all flex items-center justify-between cursor-pointer"
                :class="
                  key === activeLessonId
                    ? 'bg-primary-50/70 border-primary-200 text-primary-650 dark:bg-primary-900 dark:border-primary-900/40 dark:text-primary-400'
                    : 'bg-white border-slate-200/60 text-slate-700 dark:bg-slate-900 dark:border-slate-800/60 dark:text-slate-300 hover:bg-slate-50 dark:hover:bg-slate-700'
                "
              >
                <span class="truncate pr-1 flex items-center gap-1.5">
                  <span v-if="completedLessons[key]" class="text-emerald-500 shrink-0">✅</span>
                  <span>{{ lesson.title["es"] || lesson.title["en"] }}</span>
                </span>
              </button>

              <!-- Botón borrar lección personalizada -->
              <button
                @click.stop="$emit('delete-custom-lesson', key)"
                class="absolute right-2.5 top-1/2 -translate-y-1/2 opacity-0 group-hover:opacity-100 p-1.5 rounded-lg text-slate-400 hover:text-rose-500 hover:bg-rose-50 dark:hover:bg-rose-950/30 transition-all cursor-pointer select-none"
                title="Eliminar lección"
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
                    d="m14.74 9-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 0 1-2.244 2.077H8.084a2.25 2.25 0 0 1-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 0 0-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 0 1 3.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 0 0-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 0 0-7.5 0"
                  />
                </svg>
              </button>
            </div>
          </div>

          <!-- Práctica de voz -->
          <div class="flex flex-col gap-1.5">
            <span class="text-[9px] font-black uppercase tracking-widest text-slate-400 dark:text-slate-500 mb-1">
              Práctica de Voz
            </span>
            <button
              @click="$emit('select-lesson', 'custom_practice')"
              class="w-full text-left px-3.5 py-2.5 rounded-xl border text-xs font-bold transition-all flex items-center justify-between cursor-pointer"
              :class="
                activeLessonId === 'custom_practice'
                  ? 'bg-primary-50/70 border-primary-200 text-primary-650 dark:bg-primary-900 dark:border-primary-900/40 dark:text-primary-400'
                  : 'bg-white border-slate-200/60 text-slate-700 dark:bg-slate-900 dark:border-slate-800/60 dark:text-slate-300 hover:bg-slate-50 dark:hover:bg-slate-700'
              "
            >
              <span>Práctica por Voz (Grabadas)</span>
            </button>
          </div>

          <!-- Repaso de Frases Difíciles -->
          <div class="flex flex-col gap-1.5" v-if="difficultPhrases.length > 0">
            <span class="text-[9px] font-black uppercase tracking-widest text-slate-400 dark:text-slate-500 mb-1 flex items-center gap-1.5">
              <span>🔄</span> Repaso Espaciado
            </span>
            <button
              @click="$emit('select-lesson', 'difficult_review')"
              class="w-full text-left px-3.5 py-2.5 rounded-xl border text-xs font-bold transition-all flex items-center justify-between cursor-pointer"
              :class="
                activeLessonId === 'difficult_review'
                  ? 'bg-amber-50/70 border-amber-200 text-amber-700 dark:bg-amber-900/30 dark:border-amber-900/40 dark:text-amber-400'
                  : 'bg-white border-slate-200/60 text-slate-700 dark:bg-slate-900 dark:border-slate-800/60 dark:text-slate-300 hover:bg-slate-50 dark:hover:bg-slate-700'
              "
            >
              <span class="truncate pr-1">Frases Difíciles</span>
              <span class="shrink-0 text-[9px] font-black px-1.5 py-0.5 rounded-full bg-amber-100 text-amber-700 dark:bg-amber-950/40 dark:text-amber-400 border border-amber-200/60 dark:border-amber-800/30">
                {{ difficultPhrases.length }}
              </span>
            </button>
            <button
              @click="$emit('clear-difficult-phrases')"
              class="w-full text-center py-1.5 text-[10px] font-semibold text-slate-400 dark:text-slate-500 hover:text-rose-500 dark:hover:text-rose-400 transition-colors cursor-pointer select-none"
            >
              Limpiar todas
            </button>
          </div>
        </div>

        <!-- Mobile-hidden bottom help -->
        <div
          class="mt-auto pt-4 border-t border-slate-200/60 dark:border-slate-800/20 hidden md:block"
        >
          <button
            id="btn-show-issues"
            class="w-full py-2 bg-slate-100 hover:bg-slate-200/80 dark:bg-slate-900/30 dark:hover:bg-slate-900/60 border border-slate-200/50 dark:border-slate-800/30 rounded-xl text-3xs font-bold text-slate-500 dark:text-slate-400 transition-colors flex items-center justify-center gap-1.5 cursor-pointer select-none"
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
                d="M12 9v3.75m9-.75a9 9 0 1 1-18 0 9 9 0 0 1 18 0Zm-9 3.75h.008v.008H12v-.008Z"
              />
            </svg>
            Reportar un problema
          </button>
        </div>
      </aside>
    </Transition>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from "vue";
import rawLessonsIndex from "../data/lessons_index.json";
const lessonsIndex = rawLessonsIndex as Array<{
  id: string;
  level: string;
  category?: string;
  title: Record<string, string>;
}>;

interface Props {
  showSidebar: boolean;
  activeLessonId: string;
  completedLessons: Record<string, boolean>;
  customLessons: Record<string, any>;
  difficultPhrases: any[];
  profile: { language: string; level: string; nickname: string };
}

defineProps<Props>();

defineEmits<{
  (e: "toggle-sidebar"): void;
  (e: "select-lesson", lessonId: string): void;
  (e: "delete-custom-lesson", key: string): void;
  (e: "clear-difficult-phrases"): void;
}>();

const activeSidebarTab = ref("roadmap");

// Agrupación de lecciones para la Ruta de Aprendizaje
const lessonsByLevel = computed(() => {
  const beginner = lessonsIndex.filter((l) => l.level === "beginner");
  const intermediate = lessonsIndex.filter((l) => l.level === "intermediate");
  const advanced = lessonsIndex.filter((l) => l.level === "advanced");
  return { beginner, intermediate, advanced };
});
</script>
