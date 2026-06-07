<template>
  <div
    id="view-typing"
    :class="'theme-' + selectedTheme"
    class="grow flex flex-col md:flex-row overflow-hidden relative transition-colors duration-300"
  >
    <!-- LEFT SIDEBAR (COMPLETED LIST) -->
    <Transition name="sidebar">
      <aside
        v-if="showSidebar"
        class="w-full md:w-80 border-r border-slate-200/60 dark:border-slate-800/40 bg-slate-50/50 dark:bg-slate-950/20 p-5 flex flex-col justify-start shrink-0 relative overflow-y-auto max-h-50 md:max-h-[95vh]"
      >
        <!-- Tabs selector -->
        <div
          class="flex border-b border-slate-200/60 dark:border-slate-800/40 mb-4 select-none shrink-0"
        >
          <button
            @click="activeSidebarTab = 'lessons'"
            class="flex-1 pb-3 text-[11px] font-extrabold uppercase tracking-wider text-center cursor-pointer transition-all border-b-2"
            :class="
              activeSidebarTab === 'lessons'
                ? 'border-primary-550 text-primary-650 dark:border-primary-500 dark:text-primary-400'
                : 'border-transparent text-slate-400 dark:text-slate-500 hover:text-slate-650 dark:hover:text-slate-400'
            "
          >
            Temas
          </button>
          <button
            @click="activeSidebarTab = 'progress'"
            class="flex-1 pb-3 text-[11px] font-extrabold uppercase tracking-wider text-center cursor-pointer transition-all border-b-2 flex items-center justify-center gap-1.5"
            :class="
              activeSidebarTab === 'progress'
                ? 'border-primary-550 text-primary-650 dark:border-primary-500 dark:text-primary-400'
                : 'border-transparent text-slate-400 dark:text-slate-500 hover:text-slate-650 dark:hover:text-slate-400'
            "
          >
            <span>Progreso</span>
            <span
              class="px-1.5 py-0.5 rounded-full text-4xs font-mono font-bold"
              :class="
                activeSidebarTab === 'progress'
                  ? 'bg-primary-100 dark:bg-primary-900/60 text-primary-650 dark:text-primary-450'
                  : 'bg-slate-100 dark:bg-slate-900 text-slate-400 dark:text-slate-500'
              "
            >
              {{ completedPhrases.length }}
            </span>
          </button>
        </div>

        <!-- LESSONS LIST TAB -->
        <div
          v-if="activeSidebarTab === 'lessons'"
          class="flex flex-col gap-4 overflow-y-auto pr-1 grow select-none"
        >
          <!-- Predeterminadas -->
          <div class="flex flex-col gap-1.5">
            <span
              class="text-[9px] font-black uppercase tracking-widest text-slate-400 dark:text-slate-500 mb-1"
            >
              Temas Predeterminados
            </span>
            <button
              v-for="(lesson, key) in lessonsData"
              :key="key"
              @click="selectLesson(key)"
              class="w-full text-left px-3.5 py-2.5 rounded-xl border text-xs font-bold transition-all flex items-center justify-between cursor-pointer"
              :class="
                key === activeLessonId
                  ? 'bg-primary-50/70 border-primary-200 text-primary-650 dark:bg-primary-900 dark:border-primary-900/40 dark:text-primary-400'
                  : 'bg-white border-slate-200/60 text-slate-700 dark:bg-slate-900 dark:border-slate-800/60 dark:text-slate-300 hover:bg-slate-50 dark:hover:bg-slate-700'
              "
            >
              <span class="truncate pr-1">{{
                lesson.title[profile.language] || lesson.title["es"]
              }}</span>
            </button>
          </div>

          <!-- Creadas por el usuario -->
          <div
            class="flex flex-col gap-1.5"
            v-if="Object.keys(customLessons).length > 0"
          >
            <span
              class="text-[9px] font-black uppercase tracking-widest text-slate-400 dark:text-slate-500 mb-1"
            >
              Tus Temas Creados
            </span>
            <div
              v-for="(lesson, key) in customLessons"
              :key="key"
              class="relative group"
            >
              <button
                @click="selectLesson(key)"
                class="w-full text-left pl-3.5 pr-9 py-2.5 rounded-xl border text-xs font-bold transition-all flex items-center justify-between cursor-pointer"
                :class="
                  key === activeLessonId
                    ? 'bg-primary-50/70 border-primary-200 text-primary-650 dark:bg-primary-900 dark:border-primary-900/40 dark:text-primary-400'
                    : 'bg-white border-slate-200/60 text-slate-700 dark:bg-slate-900 dark:border-slate-800/60 dark:text-slate-300 hover:bg-slate-50 dark:hover:bg-slate-700'
                "
              >
                <span class="truncate pr-1">{{
                  lesson.title["es"] || lesson.title["en"]
                }}</span>
              </button>

              <!-- Botón borrar lección personalizada -->
              <button
                @click.stop="deleteCustomLesson(key)"
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
            <span
              class="text-[9px] font-black uppercase tracking-widest text-slate-400 dark:text-slate-500 mb-1"
            >
              Práctica de Voz
            </span>
            <button
              @click="selectLesson('custom_practice')"
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

          <div
            class="h-px bg-slate-200/50 dark:bg-slate-800/40 my-1 shrink-0"
          ></div>

          <!-- Botón Crear Nueva Lección -->
          <button
            @click="selectLesson('create_new_lesson')"
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
        </div>

        <!-- PROGRESS TAB -->
        <div
          v-if="activeSidebarTab === 'progress'"
          class="flex flex-col gap-3 overflow-y-auto pr-1 grow"
        >
          <div
            v-if="completedPhrases.length === 0"
            class="py-12 text-center text-slate-400 dark:text-slate-650 text-xs font-medium"
          >
            Escribe la frase central para completarla.
          </div>
          <div
            v-else
            v-for="(p, idx) in completedPhrases"
            :key="idx"
            class="p-3 rounded-xl bg-white dark:bg-slate-900 border border-slate-200/60 dark:border-slate-800/40 shadow-xs flex flex-col gap-1 select-text transition-colors duration-300 animate-in fade-in"
          >
            <div class="flex items-start gap-2">
              <span class="text-3xs font-mono font-bold text-primary-500 mt-0.5"
                >{{ idx + 1 }}.</span
              >
              <div>
                <p class="text-xs font-bold text-slate-800 dark:text-slate-100">
                  <span
                    v-if="p.speakerPrefix"
                    class="text-primary-600 dark:text-primary-400 mr-1.5 font-bold tracking-wide text-2xs"
                    >{{ p.speakerPrefix }}</span
                  >{{ p.text }}
                </p>
                <p class="text-2xs text-slate-400 dark:text-slate-500">
                  {{ p.translation }}
                </p>
              </div>
            </div>
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

    <!-- CENTRAL TYPING AREA -->
    <section
      class="grow flex flex-col justify-between p-6 md:px-12 md:pb-12 md:pt-6 items-center relative overflow-hidden select-none"
    >
      <!-- Lesson Info / Topic Selector -->
      <div
        class="w-full max-w-6xl flex justify-between items-center gap-4 border-b border-slate-200/60 dark:border-slate-800/30 pb-4 mb-4"
      >
        <!-- Toggle Sidebar Button integrated aesthetics -->
        <button
          @click="toggleSidebar"
          class="p-2 rounded-xl border transition-all flex items-center justify-center cursor-pointer select-none gap-1.5"
          :class="
            showSidebar
              ? 'bg-primary-50 border-primary-200/60 text-primary-650 dark:bg-primary-950/20 dark:border-primary-900/30 dark:text-primary-400'
              : 'bg-slate-100 border-slate-200/50 text-slate-500 dark:bg-slate-900/30 dark:border-slate-800/30 dark:text-slate-400 hover:bg-slate-200/80 dark:hover:bg-slate-900/60'
          "
          :title="showSidebar ? 'Hide' : 'Show'"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="2.3"
            stroke="currentColor"
            class="w-4 h-4"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M9 17.25v-10.5M3 5.25h18a1.5 1.5 0 0 1 1.5 1.5v10.5a1.5 1.5 0 0 1-1.5 1.5H3a1.5 1.5 0 0 1-1.5-1.5V6.75a1.5 1.5 0 0 1 1.5-1.5Z"
            />
          </svg>
          <span
            v-if="!showSidebar"
            class="text-xs font-semibold text-slate-400 dark:text-slate-500 loc-topic-prefix"
            >Show</span
          >
          <span
            v-if="showSidebar"
            class="text-xs font-semibold text-slate-400 dark:text-slate-500 loc-topic-prefix"
            >Hide</span
          >
        </button>

        <div class="flex items-center gap-2" id="lesson-select-wrapper">
          <span
            class="text-xs font-semibold text-slate-400 dark:text-slate-500 uppercase tracking-wider"
          >
            Tema:
          </span>
          <span
            class="text-sm font-extrabold text-primary-650 dark:text-primary-400 select-text"
          >
            {{ activeLessonLabel }}
          </span>
        </div>

        <div class="flex items-center gap-2">
          <!-- Modo Escucha / Dictado Toggle -->
          <button
            id="btn-listening-mode"
            @click="isListeningMode = !isListeningMode"
            class="p-2 rounded-lg transition-colors flex items-center justify-center gap-1.5 text-xs font-bold cursor-pointer select-none border"
            :class="
              isListeningMode
                ? 'bg-amber-50 border-amber-200/60 text-amber-650 dark:bg-amber-950/30 dark:border-amber-900/20 dark:text-amber-400'
                : 'bg-slate-100 border-slate-200/50 text-slate-500 dark:bg-slate-900/30 dark:border-slate-800/30 dark:text-slate-400 hover:bg-slate-200/80 dark:hover:bg-slate-900/60'
            "
            :title="
              isListeningMode
                ? 'Modo Escucha Activo'
                : 'Activar Modo Escucha (Dictado)'
            "
          >
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
                d="M9 9a3 3 0 1 1 6 0M3 12a9 9 0 0 1 18 0M3 12h3.75M20.25 12h-3.75M6.75 12v3.75a3 3 0 0 0 6 0V12m-6 0h6m0 0v3.75a3 3 0 0 0 6 0V12"
              />
            </svg>
            <span class="hidden sm:inline">{{
              isListeningMode ? "Dictado activo" : "Modo Escucha"
            }}</span>
          </button>

          <!-- Modo Ciego / Blind Mode Toggle -->
          <button
            id="btn-blind-mode"
            @click="isBlindMode = !isBlindMode"
            class="p-2 rounded-lg transition-colors flex items-center justify-center gap-1.5 text-xs font-bold cursor-pointer select-none border"
            :class="
              isBlindMode
                ? 'bg-indigo-50 border-indigo-200/60 text-indigo-650 dark:bg-indigo-950/30 dark:border-indigo-900/20 dark:text-indigo-400 shadow-xs'
                : 'bg-slate-100 border-slate-200/50 text-slate-500 dark:bg-slate-900/30 dark:border-slate-800/30 dark:text-slate-400 hover:bg-slate-200/80 dark:hover:bg-slate-900/60'
            "
            :title="
              isBlindMode
                ? 'Modo Ciego Activo'
                : 'Activar Modo Ciego (Oculta texto y traducción)'
            "
          >
            <span>🙈</span>
            <span class="hidden sm:inline">{{
              isBlindMode ? "Ciego activo" : "Modo Ciego"
            }}</span>
          </button>

          <!-- Botón de Pista para Modo Ciego (solo visible en modo ciego) -->
          <button
            v-if="isBlindMode"
            id="btn-blind-hint"
            @click="showBlindHint = !showBlindHint"
            class="p-2 rounded-lg transition-colors flex items-center justify-center gap-1.5 text-xs font-bold cursor-pointer select-none border"
            :class="
              showBlindHint
                ? 'bg-emerald-50 border-emerald-200/60 text-emerald-650 dark:bg-emerald-950/30 dark:border-emerald-900/20 dark:text-emerald-400 shadow-xs'
                : 'bg-slate-100 border-slate-200/50 text-slate-500 dark:bg-slate-900/30 dark:border-slate-800/30 dark:text-slate-400 hover:bg-slate-200/80 dark:hover:bg-slate-900/60'
            "
            title="Mostrar/Ocultar traducción de apoyo"
          >
            <span>{{ showBlindHint ? '👁️' : '👁️‍🗨️' }}</span>
            <span class="hidden sm:inline">Pista</span>
          </button>

          <!-- Replay voice -->
          <button
            id="btn-replay-audio"
            @click="replayAudio"
            class="p-2 rounded-lg bg-primary-50 dark:bg-primary-950/30 text-primary-600 dark:text-primary-400 hover:bg-primary-100/60 dark:hover:bg-primary-900/50 transition-colors flex items-center justify-center gap-1.5 text-xs font-bold cursor-pointer select-none border border-primary-100/50 dark:border-primary-900/20"
            title="Replay (Shortcut: \)"
          >
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
                d="M19.114 5.636a9 9 0 0 1 0 12.728M16.463 8.288a5.25 5.25 0 0 1 0 7.424M6.75 8.25l4.72-4.72a.75.75 0 0 1 1.28.53v15.88a.75.75 0 0 1-1.28.53l-4.72-4.72H4.51c-.88 0-1.704-.507-1.938-1.354A9.009 9.009 0 0 1 2.25 12c0-.83.112-1.633.322-2.396C2.806 8.756 3.63 8.25 4.51 8.25H6.75Z"
              />
            </svg>
            <span class="hidden sm:inline">Repetir audio</span>
          </button>

          <!-- Voice settings dropdown container -->
          <div class="relative" id="voice-settings-wrapper">
            <button
              @click="isVoiceSettingsOpen = !isVoiceSettingsOpen"
              class="p-2 rounded-lg transition-colors flex items-center justify-center cursor-pointer select-none border"
              :class="
                isVoiceSettingsOpen
                  ? 'bg-primary-50 border-primary-200/60 text-primary-650 dark:bg-primary-950/20 dark:border-primary-900/30 dark:text-primary-400'
                  : 'bg-slate-100 border-slate-200/50 text-slate-500 dark:bg-slate-900/30 dark:border-slate-800/30 dark:text-slate-400 hover:bg-slate-200/80 dark:hover:bg-slate-900/60'
              "
              title="Configuración de Voz (TTS)"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
                stroke-width="2.3"
                stroke="currentColor"
                class="w-4 h-4"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  d="M9.594 3.94c.09-.542.56-.94 1.11-.94h2.593c.55 0 1.02.398 1.11.94l.213 1.281c.063.374.313.686.645.87.074.04.147.083.22.127.324.196.72.257 1.075.124l1.217-.456a1.125 1.125 0 0 1 1.37.49l1.296 2.247a1.125 1.125 0 0 1-.26 1.43l-1.003.828c-.293.241-.438.613-.43.992a7.723 7.723 0 0 1 0 .255c-.008.378.137.75.43.991l1.004.827a1.125 1.125 0 0 1 .26 1.43l-1.297 2.247a1.125 1.125 0 0 1-1.369.491l-1.217-.456c-.355-.133-.75-.072-1.076.124a6.47 6.47 0 0 1-.22.128c-.331.183-.581.495-.644.869l-.213 1.281c-.09.543-.56.94-1.11.94h-2.594c-.55 0-1.019-.398-1.11-.94l-.213-1.281c-.062-.374-.312-.686-.644-.87a6.52 6.52 0 0 1-.22-.127c-.325-.196-.72-.257-1.076-.124l-1.217.456a1.125 1.125 0 0 1-1.369-.49l-1.297-2.247a1.125 1.125 0 0 1 .26-1.43l1.004-.827c.292-.24.437-.613.43-.991a6.936 6.936 0 0 1 0-.255c.007-.38-.138-.751-.43-.992l-1.004-.827a1.125 1.125 0 0 1-.26-1.43l1.297-2.247a1.125 1.125 0 0 1 1.37-.491l1.216.456c.356.133.751.072 1.076-.124.072-.044.146-.086.22-.128.332-.183.582-.495.644-.869l.214-1.28Z"
                />
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  d="M15 12a3 3 0 1 1-6 0 3 3 0 0 1 6 0Z"
                />
              </svg>
            </button>

            <!-- Floating Voice Settings Dropdown Menu -->
            <Transition name="dropdown">
              <div
                v-if="isVoiceSettingsOpen"
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
                    v-model="selectedVoiceName"
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
                    v-model.number="voiceRate"
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
                    v-model.number="voicePitch"
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
                    v-model.number="voiceVolume"
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
                      @click="useGeminiTts = !useGeminiTts"
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
                    v-model="geminiApiKey"
                    placeholder="Almacenada localmente"
                    class="w-full bg-slate-50 dark:bg-slate-950 border border-slate-200 dark:border-slate-800 rounded-xl px-3 py-2 text-xs font-semibold text-slate-700 dark:text-slate-200 outline-none"
                  />
                </div>

                <!-- Efectos de Sonido (Idea 5) -->
                <div
                  class="border-t border-slate-100 dark:border-slate-850 my-1 pt-3 flex flex-col gap-1.5"
                >
                  <label
                    class="text-3xs font-extrabold uppercase text-slate-400 dark:text-slate-500 tracking-wider select-none"
                    >Efectos de Sonido (Mecanografía)</label
                  >
                  <select
                    v-model="selectedSoundTheme"
                    class="w-full bg-slate-50 dark:bg-slate-950 border border-slate-200 dark:border-slate-800 rounded-xl px-3 py-2.5 text-xs font-semibold text-slate-700 dark:text-slate-200 outline-none cursor-pointer"
                  >
                    <option value="none">Sin sonido</option>
                    <option value="mechanical">Teclado Mecánico (Click)</option>
                    <option value="bubble">Burbuja Digital (Bubble)</option>
                    <option value="retro">Retro Game (Beep)</option>
                  </select>
                </div>

                <!-- Temas Visuales (Idea 5) -->
                <div
                  class="border-t border-slate-100 dark:border-slate-850 my-1 pt-3 flex flex-col gap-1.5"
                >
                  <label
                    class="text-3xs font-extrabold uppercase text-slate-400 dark:text-slate-500 tracking-wider select-none"
                    >Tema Visual</label
                  >
                  <select
                    v-model="selectedTheme"
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
                    v-model="selectedAiModel"
                    class="w-full bg-slate-50 dark:bg-slate-950 border border-slate-200 dark:border-slate-800 rounded-xl px-3 py-2.5 text-xs font-semibold text-slate-700 dark:text-slate-200 outline-none cursor-pointer"
                  >
                    <option value="gemini-2.5-flash">Gemini 2.5 Flash (Alta cuota, recomendado)</option>
                    <option value="gemini-1.5-flash">Gemini 1.5 Flash (Estable, alta cuota)</option>
                    <option value="gemini-3.5-flash">Gemini 3.5 Flash (Experimental, cuota limitada)</option>
                  </select>
                </div>
              </div>
            </Transition>
          </div>
        </div>
      </div>
      <!-- HIDDEN INPUT FOR DRIVING THE KEYBOARD ON MOBILE/DESKTOP -->
      <input
        id="typing-hidden-input"
        type="text"
        ref="inputRef"
        v-model="typedText"
        @input="processInput"
        @focus="isFocused = true"
        @blur="isFocused = false"
        autocomplete="off"
        autocorrect="off"
        autocapitalize="none"
        spellcheck="false"
        class="absolute opacity-0 -z-50 pointer-events-none"
      />

      <div
        v-if="activeLessonId !== 'create_new_lesson'"
        class="grow flex flex-col justify-between w-full max-w-6xl items-left"
      >
        <!-- TARGET TYPING TEXT CON        <!-- TARGET TYPING TEXT CONTAINER WITH NAVIGATION BUTTONS -->
        <div class="w-full grow flex items-center justify-between gap-4 py-8">
          <!-- Botón frase anterior -->
          <button
            @click="prevPhrase"
            :disabled="activePhraseIndex === 0"
            class="p-3.5 rounded-full border border-slate-200/60 dark:border-slate-800/40 bg-white/70 dark:bg-slate-900/65 hover:bg-slate-100 dark:hover:bg-slate-850 text-slate-450 dark:text-slate-500 disabled:opacity-20 disabled:hover:bg-transparent dark:disabled:hover:bg-transparent transition-all cursor-pointer disabled:cursor-not-allowed select-none shrink-0"
            title="Frase anterior"
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
                d="M15.75 19.5 8.25 12l7.5-7.5"
              />
            </svg>
          </button>

          <!-- TARGET TYPING TEXT CONTAINER -->
          <div
            id="typing-box-container"
            @click="focusInput"
            class="grow flex flex-col justify-center items-left py-12 relative cursor-text min-h-[200px]"
          >
            <!-- Keyword Highlight -->
            <div
              v-if="showKeywordHighlight"
              id="target-phrase-keyword"
              class="w-full text-left mb-3.5 text-xs sm:text-sm font-semibold tracking-wide transition-all duration-300 select-none animate-in fade-in"
            >
              <span
                id="keyword-english"
                class="text-primary-600 dark:text-primary-400 mr-1.5"
                >{{ activePhrase?.keyword }}</span
              >
              <span
                id="keyword-translation"
                class="text-slate-400 dark:text-slate-500 font-normal"
              >
                {{ activePhrase?.keywordTranslations?.join(" • ") }}
              </span>
            </div>
            <div v-else class="w-full h-5 mb-3.5"></div>

            <!-- Speaker Role/Prefix Indicator -->
            <div
              v-if="activePhrase?.speakerPrefix"
              class="mb-3.5 px-3 py-1 w-max rounded-full text-3xs sm:text-2xs font-extrabold uppercase tracking-widest bg-primary-50 dark:bg-primary-950/40 text-primary-650 dark:text-primary-400 border border-primary-100/50 dark:border-primary-800/30 select-none animate-in-fade"
            >
              {{ activePhrase.speakerPrefix }}
            </div>

            <!-- Text to type -->
            <div
              id="target-phrase-letters"
              class="text-5xl md:text-6xl font-bold tracking-normal font-mono leading-relaxed mb-4 select-none flex flex-wrap justify-left gap-x-4"
            >
              <span
                v-for="(wordObj, wIdx) in wordSpans"
                :key="wIdx"
                class="word-span-interactive hover:text-primary-600 dark:hover:text-primary-400 hover:underline decoration-primary-400/60 dark:decoration-primary-650/50 decoration-wavy decoration-3 transition-all cursor-help relative inline-block"
                @mouseenter="onWordHoverPronounce(wordObj.wordText)"
                @mouseleave="onWordLeavePronounce"
                @click.stop="lookupWord(wordObj.wordText, activePhrase!.text)"
              >
                <span
                  v-for="(letterObj, lIdx) in wordObj.letters"
                  :key="lIdx"
                  :class="letterObj.class"
                >
                  {{ letterObj.char }}
                </span>
              </span>
            </div>

            <!-- Native Translation -->
            <div
              id="target-phrase-translation"
              class="text-base md:text-3xl text-slate-400 dark:text-slate-500 font-medium text-left h-8 leading-relaxed"
            >
              {{ activeTranslation }}
            </div>

            <!-- Word Dictionary Hover Card -->
            <Transition name="dropdown">
              <div
                v-if="activeWordDefinition"
                id="word-dictionary-card"
                class="absolute bottom-4 z-35 w-full max-w-sm bg-white/95 dark:bg-slate-900/95 border border-slate-200 dark:border-slate-800 shadow-xl rounded-2xl p-4 flex flex-col gap-1.5 backdrop-blur-md text-left select-text relative"
              >
                <!-- Botón de Cerrar (x) -->
                <button
                  @click.stop="closeDictionary"
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
                      {{ activeWordDefinition.word }}
                    </span>

                    <span
                      v-if="activeWordDefinition.phonetic"
                      class="text-4xs font-mono text-slate-400 dark:text-slate-500 font-bold"
                    >
                      {{ activeWordDefinition.phonetic }}
                    </span>

                    <button
                      v-if="activeWordDefinition.audioUrl"
                      @click.stop="playWordAudio(activeWordDefinition.audioUrl)"
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
                      v-if="activeWordDefinition.partOfSpeech"
                      class="text-4xs font-mono font-bold text-primary-500 uppercase tracking-widest bg-primary-50 dark:bg-primary-950/40 border border-primary-100/30 dark:border-primary-900/20 px-1.5 py-0.5 rounded-md"
                    >
                      {{ activeWordDefinition.partOfSpeech }}
                    </span>
                  </span>
                  <svg
                    v-if="isLoadingDefinition"
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
                  {{ activeWordDefinition.definition }}
                </p>
                <p
                  v-if="activeWordDefinition.translation"
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
                  {{ activeWordDefinition.translation }}
                </p>
              </div>
            </Transition>
          </div>

          <!-- Botón frase siguiente (Omitir) -->
          <button
            @click="skipPhrase"
            class="p-3.5 rounded-full border border-slate-200/60 dark:border-slate-800/40 bg-white/70 dark:bg-slate-900/65 hover:bg-slate-100 dark:hover:bg-slate-850 text-slate-450 dark:text-slate-500 transition-all cursor-pointer select-none shrink-0"
            title="Siguiente frase (Omitir)"
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
                d="m8.25 4.5 7.5 7.5-7.5 7.5"
              />
            </svg>
          </button>
        </div>

        <!-- STATS BAR -->
        <div
          class="w-full max-w-6xl flex flex-col sm:flex-row justify-between items-center gap-4 mt-4 pt-4 border-t border-slate-200/60 dark:border-slate-800/30 text-xs"
        >
          <!-- Bottom Stats -->
          <div
            class="flex items-center gap-2 px-4 py-1.5 rounded-full border border-slate-200/50 dark:border-slate-800/30 bg-white/70 dark:bg-slate-900/65 backdrop-blur-md shadow-xs"
          >
            <div class="flex items-center gap-1">
              <span
                id="live-wpm"
                class="font-mono text-base font-bold text-slate-800 dark:text-slate-100"
                >{{ liveWpm }}</span
              >
              <span
                class="text-3xs font-semibold text-slate-400 dark:text-slate-500 tracking-wider"
                >WPM</span
              >
            </div>
            <span class="text-slate-200 dark:text-slate-850">|</span>
            <div class="flex items-center gap-1">
              <span
                id="live-acc"
                class="font-mono text-base font-bold text-slate-800 dark:text-slate-100"
                >{{ liveAcc }}%</span
              >
              <span
                class="text-3xs font-semibold text-slate-400 dark:text-slate-500 tracking-wider"
                >ACC</span
              >
            </div>
          </div>

          <!-- Helper instructions -->
          <div
            class="text-slate-400 dark:text-slate-500 font-semibold text-[11px] flex items-center gap-2"
          >
            <span>presiona</span>
            <kbd
              class="px-1.5 py-0.5 rounded bg-slate-200 dark:bg-slate-800 border border-slate-300 dark:border-slate-700 text-3xs font-mono text-slate-500 dark:text-slate-400"
              >\</kbd
            >
            <span class="loc-hint-repeat">{{ t.repeat }}</span>
          </div>
        </div>

        <!-- LESSON COMPLETE OVERLAY SCREEN -->
        <div
          v-if="showCompleteOverlay"
          id="lesson-complete-overlay"
          class="absolute inset-0 bg-slate-50/98 dark:bg-slate-950/98 z-40 flex flex-col items-center justify-center p-6 animate-fade-in"
        >
          <div
            class="glass p-8 rounded-3xl border border-white/20 dark:border-white/5 shadow-2xl max-w-md w-full text-center flex flex-col items-center gap-6 relative overflow-hidden"
          >
            <div
              class="absolute top-0 left-0 right-0 h-2 bg-gradient-to-r from-primary-500 via-primary-400 to-primary-300"
            ></div>

            <div
              class="p-4 bg-emerald-50 dark:bg-emerald-900/30 text-emerald-500 dark:text-emerald-400 rounded-full"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
                stroke-width="2.5"
                stroke="currentColor"
                class="w-10 h-10 animate-bounce"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  d="m4.5 12.75 6 6 9-13.5"
                />
              </svg>
            </div>

            <div>
              <h3
                class="text-2xl font-black font-display tracking-tight text-slate-800 dark:text-white loc-compl-congrats"
              >
                {{ t.congrats }}
              </h3>
              <p
                class="text-xs text-slate-550 dark:text-slate-400 mt-1 loc-compl-desc"
              >
                {{ t.desc }}
              </p>
            </div>

            <!-- Stats grid -->
            <div
              class="grid grid-cols-2 gap-4 w-full p-4 rounded-2xl bg-slate-100/50 dark:bg-slate-900/50 border border-slate-200/50 dark:border-slate-800/30"
            >
              <div class="flex flex-col items-center">
                <span
                  class="text-3xs text-slate-400 dark:text-slate-500 uppercase tracking-wider font-semibold"
                  >Velocidad</span
                >
                <span
                  id="final-wpm"
                  class="text-2xl font-bold font-mono text-primary-600 dark:text-primary-400"
                  >{{ finalWpmComputed }} WPM</span
                >
              </div>
              <div class="flex flex-col items-center">
                <span
                  class="text-3xs text-slate-400 dark:text-slate-500 uppercase tracking-wider font-semibold"
                  >Precisión</span
                >
                <span
                  id="final-acc"
                  class="text-2xl font-bold font-mono text-emerald-600 dark:text-emerald-400"
                  >{{ finalAccComputed }}%</span
                >
              </div>
            </div>

            <!-- Action buttons -->
            <div class="flex gap-3 w-full">
              <button
                id="btn-lesson-restart"
                @click="restartLesson"
                class="flex-1 py-3 px-4 border border-slate-200 dark:border-slate-800 glass hover:bg-slate-100 dark:hover:bg-slate-900 rounded-xl font-bold text-xs transition-all cursor-pointer text-slate-700 dark:text-slate-350 select-none"
              >
                Repetir Lección
              </button>
              <button
                id="btn-lesson-next"
                @click="nextLesson"
                class="flex-1 py-3 px-4 bg-primary-650 hover:bg-primary-700 dark:hover:bg-primary-600 text-white rounded-xl font-bold text-xs shadow-lg shadow-primary-500/10 transition-all cursor-pointer select-none"
              >
                Siguiente Tema
              </button>
            </div>
          </div>
        </div>
      </div>

      <!-- CREADOR DE LECCIONES PERSONALIZADAS -->
      <div
        v-else
        class="w-full max-w-2xl grow flex flex-col justify-center py-6 animate-fade-in text-left"
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
          <div class="flex border-b border-slate-100 dark:border-slate-850 select-none shrink-0 mb-2">
            <button
              type="button"
              @click="creationTab = 'paste'"
              class="flex-1 pb-3 text-xs font-extrabold uppercase tracking-wider text-center cursor-pointer transition-all border-b-2"
              :class="
                creationTab === 'paste'
                  ? 'border-primary-550 text-primary-650 dark:border-primary-500 dark:text-primary-400'
                  : 'border-transparent text-slate-400 dark:text-slate-500 hover:text-slate-600 dark:hover:text-slate-450'
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
              <span class="px-1.5 py-0.5 rounded-full text-[9px] bg-amber-100 dark:bg-amber-950/40 text-amber-650 dark:text-amber-450 font-bold uppercase tracking-wider">
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
              <label class="text-3xs font-extrabold uppercase text-slate-400 dark:text-slate-500 tracking-wider">
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
              <label class="text-3xs font-extrabold uppercase text-slate-400 dark:text-slate-500 tracking-wider">
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
              :disabled="
                isGenerating ||
                !expressTopic ||
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
              @click="cancelCreateLesson"
              class="py-3 px-4 text-slate-400 dark:text-slate-500 hover:text-slate-650 dark:hover:text-slate-350 font-bold text-xs rounded-xl transition-all cursor-pointer select-none"
            >
              Cancelar
            </button>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, nextTick, onMounted, onUnmounted, watch } from "vue";
import { GoogleGenAI } from "@google/genai";

// Types definition
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
  phrases: PhraseTranslation[];
}

import newFriendLesson from "../data/lessons/new_friend.json";
import orderingFoodLesson from "../data/lessons/ordering_food.json";
import travelingLesson from "../data/lessons/traveling.json";
import jobInterviewLesson from "../data/lessons/job_interview.json";

// Data sources
const lessonsData: Record<string, Lesson> = {
  new_friend: newFriendLesson as Lesson,
  ordering_food: orderingFoodLesson as Lesson,
  traveling: travelingLesson as Lesson,
  job_interview: jobInterviewLesson as Lesson,
};

const tLocalMap: Record<string, Record<string, string>> = {
  es: {
    congrats: "¡Lección Completada!",
    desc: "¡Excelente trabajo practicando tu inglés!",
    repeat: "para repetir el audio",
    topic: "Tema:",
  },
  ar: {
    congrats: "اكتمل الدرس!",
    desc: "عمل رائع في ممارسة لغتك الإنجليزية!",
    repeat: "لتكرار الصوت",
    topic: "الموضوع:",
  },
  pt: {
    congrats: "Lição Concluída!",
    desc: "Excelente trabalho praticando seu inglês!",
    repeat: "para repetir o áudio",
    topic: "Tema:",
  },
  fr: {
    congrats: "Leçon terminée !",
    desc: "Excellent travail de pratique de l'anglais !",
    repeat: "pour répéter l'audio",
    topic: "Sujet :",
  },
  de: {
    congrats: "Lektion abgeschlossen!",
    desc: "Hervorragende Arbeit beim Englisch üben!",
    repeat: "um Audio zu wiederholen",
    topic: "Thema:",
  },
  it: {
    congrats: "Lezione Completata!",
    desc: "Ottimo lavoro nel praticare il tuo inglese!",
    repeat: "per ripetere l'audio",
    topic: "Argomento:",
  },
  en: {
    congrats: "Lesson Completed!",
    desc: "Great job practicing your English!",
    repeat: "to repeat audio",
    topic: "Topic:",
  },
};

// Reactivity states
const activeLessonId = ref("new_friend");
const isDropdownOpen = ref(false);
const activePhraseIndex = ref(0);
const typedText = ref("");
const errorsCount = ref(0);
const startTime = ref(0);
const isTimerRunning = ref(false);
const completedPhrases = ref<
  { text: string; translation: string; speakerPrefix?: string }[]
>([]);
const profile = ref({ language: "es", level: "beginner", nickname: "" });

// Listening Mode & Hover Dictionary States
const isListeningMode = ref(false);

interface WordDefinition {
  word: string;
  partOfSpeech: string;
  definition: string;
  translation: string;
  phonetic?: string;
  audioUrl?: string;
}

const activeWordDefinition = ref<WordDefinition | null>(null);
const isLoadingDefinition = ref(false);
const definitionsCache = ref<Record<string, WordDefinition>>({});

// Timer for hover debounce
let hoverTimer: number | null = null;

const inputRef = ref<HTMLInputElement | null>(null);
const sidebarRef = ref<HTMLDivElement | null>(null);
const isFocused = ref(false);

const liveWpm = ref(0);
const liveAcc = ref(100);
let lastTypedLength = 0;

const showSidebar = ref(true);
const activeSidebarTab = ref("lessons"); // 'lessons' o 'progress'

const isVoiceSettingsOpen = ref(false);
const voices = ref<SpeechSynthesisVoice[]>([]);
const selectedVoiceName = ref("");
const voiceRate = ref(0.9);
const voicePitch = ref(1.0);
const voiceVolume = ref(1.0);
const geminiApiKey = ref("");
const useGeminiTts = ref(false);
let activeGeminiAudio: HTMLAudioElement | null = null;
const audioCache = ref<Record<string, HTMLAudioElement>>({});

function getVoiceScore(name: string): number {
  const lowerName = name.toLowerCase();
  let score = 0;
  if (lowerName.includes("natural")) score += 10;
  if (lowerName.includes("online")) score += 8;
  if (lowerName.includes("google")) score += 6;
  if (lowerName.includes("neural")) score += 5;
  if (lowerName.includes("premium")) score += 3;
  return score;
}

function loadVoices() {
  if (typeof window === "undefined" || !window.speechSynthesis) return;
  const allVoices = window.speechSynthesis.getVoices();
  const filtered = allVoices.filter((v) =>
    v.lang.toLowerCase().startsWith("en")
  );

  // Ordenar voces por calidad (mayor puntuación primero)
  voices.value = filtered.sort(
    (a, b) => getVoiceScore(b.name) - getVoiceScore(a.name)
  );

  const storedVoice = localStorage.getItem("lbl_preferred_voice");
  if (storedVoice && voices.value.some((v) => v.name === storedVoice)) {
    selectedVoiceName.value = storedVoice;
  } else if (voices.value.length > 0) {
    const preferred =
      voices.value.find(
        (v) => v.name.includes("Google") || v.name.includes("Natural")
      ) || voices.value[0];
    selectedVoiceName.value = preferred.name;
  }

  const storedRate = localStorage.getItem("lbl_preferred_rate");
  if (storedRate) voiceRate.value = parseFloat(storedRate);

  const storedPitch = localStorage.getItem("lbl_preferred_pitch");
  if (storedPitch) voicePitch.value = parseFloat(storedPitch);

  const storedVolume = localStorage.getItem("lbl_preferred_volume");
  if (storedVolume) voiceVolume.value = parseFloat(storedVolume);

  const storedApiKey = localStorage.getItem("lbl_gemini_api_key");
  if (storedApiKey) geminiApiKey.value = storedApiKey;

  const storedUseGeminiTts = localStorage.getItem("lbl_use_gemini_tts");
  if (storedUseGeminiTts) useGeminiTts.value = storedUseGeminiTts === "true";
}

watch(selectedVoiceName, (newVal) => {
  if (newVal) localStorage.setItem("lbl_preferred_voice", newVal);
});
watch(voiceRate, (newVal) => {
  localStorage.setItem("lbl_preferred_rate", newVal.toString());
});
watch(voicePitch, (newVal) => {
  localStorage.setItem("lbl_preferred_pitch", newVal.toString());
});
watch(voiceVolume, (newVal) => {
  localStorage.setItem("lbl_preferred_volume", newVal.toString());
});
watch(geminiApiKey, (newVal) => {
  localStorage.setItem("lbl_gemini_api_key", newVal);
});
watch(useGeminiTts, (newVal) => {
  localStorage.setItem("lbl_use_gemini_tts", String(newVal));
});
watch(showSidebar, (newVal) => {
  localStorage.setItem("lbl_show_sidebar", String(newVal));
});

function toggleSidebar() {
  showSidebar.value = !showSidebar.value;
}

// Load user profile from LocalStorage
function readLocalProfile() {
  const stored = localStorage.getItem("lbl_user_profile");
  if (stored) {
    try {
      profile.value = JSON.parse(stored);
    } catch (e) {}
  }
}

// Compute active localized strings
const t = computed(() => {
  const lang = profile.value.language;
  return tLocalMap[lang] || tLocalMap["es"];
});

// Custom lessons reactivity states
const customLessons = ref<Record<string, Lesson>>({});
const newLessonTitle = ref("");
const newLessonText = ref("");
const contentType = ref("text"); // 'text', 'song', 'song_translated', 'dialogue'
const isGenerating = ref(false);
const errorMessage = ref("");
const importFileInput = ref<HTMLInputElement | null>(null);

// Variables de creación rápidas e interactivas
const creationTab = ref("paste"); // 'paste' o 'topic'
const expressTopic = ref("");

// Variables del Modo Ciego y de Ayuda Visual
const isBlindMode = ref(false);
const showBlindHint = ref(false);

// Variables de Estética y Sonidos (inicializados desde LocalStorage)
const selectedSoundTheme = ref(
  (typeof window !== "undefined" && localStorage.getItem("lbl_sound_theme")) || "bubble"
);
const selectedTheme = ref(
  (typeof window !== "undefined" && localStorage.getItem("lbl_visual_theme")) || "default"
);
const selectedAiModel = ref(
  (typeof window !== "undefined" && localStorage.getItem("lbl_ai_model")) || "gemini-2.5-flash"
);

// Sincronizar Modo Ciego con el Modo Escucha
watch(isBlindMode, (newVal) => {
  if (newVal) {
    isListeningMode.value = true;
  }
});

// Guardar preferencias de Temas, Sonidos y Modelo de IA
watch(selectedTheme, (newVal) => {
  localStorage.setItem("lbl_visual_theme", newVal);
});
watch(selectedSoundTheme, (newVal) => {
  localStorage.setItem("lbl_sound_theme", newVal);
});
watch(selectedAiModel, (newVal) => {
  localStorage.setItem("lbl_ai_model", newVal);
});

// Síntesis de Sonido en Tiempo Real con Web Audio API (100% Offline, Cero Latencia)
let audioCtx: AudioContext | null = null;
function getAudioContext() {
  if (!audioCtx) {
    audioCtx = new (window.AudioContext || (window as any).webkitAudioContext)();
  }
  if (audioCtx.state === "suspended") {
    audioCtx.resume();
  }
  return audioCtx;
}

function playKeyPressSound(type: "correct" | "incorrect" | "complete") {
  if (selectedSoundTheme.value === "none") return;
  try {
    const ctx = getAudioContext();
    const now = ctx.currentTime;
    
    if (type === "incorrect") {
      // Sonido de error: Onda de sierra de baja frecuencia
      const osc = ctx.createOscillator();
      const gain = ctx.createGain();
      osc.type = "sawtooth";
      osc.frequency.setValueAtTime(140, now);
      osc.frequency.exponentialRampToValueAtTime(80, now + 0.15);
      gain.gain.setValueAtTime(0.15, now);
      gain.gain.exponentialRampToValueAtTime(0.01, now + 0.15);
      osc.connect(gain);
      gain.connect(ctx.destination);
      osc.start(now);
      osc.stop(now + 0.15);
      return;
    }
    
    if (type === "complete") {
      // Arpegio ascendente triunfal al completar la frase
      const notes = [523.25, 659.25, 783.99, 1046.50]; // C5, E5, G5, C6
      notes.forEach((freq, idx) => {
        const time = now + idx * 0.06;
        const osc = ctx.createOscillator();
        const gain = ctx.createGain();
        osc.type = "sine";
        osc.frequency.setValueAtTime(freq, time);
        gain.gain.setValueAtTime(0.1, time);
        gain.gain.exponentialRampToValueAtTime(0.01, time + 0.15);
        osc.connect(gain);
        gain.connect(ctx.destination);
        osc.start(time);
        osc.stop(time + 0.18);
      });
      return;
    }
    
    if (selectedSoundTheme.value === "mechanical") {
      // Click de teclado mecánico: Mezcla de clic agudo y golpe de tecla
      const noise = ctx.createBufferSource();
      const bufferSize = ctx.sampleRate * 0.02; // 20ms
      const buffer = ctx.createBuffer(1, bufferSize, ctx.sampleRate);
      const data = buffer.getChannelData(0);
      for (let i = 0; i < bufferSize; i++) {
        data[i] = Math.random() * 2 - 1;
      }
      noise.buffer = buffer;
      const filter = ctx.createBiquadFilter();
      filter.type = "bandpass";
      filter.frequency.setValueAtTime(3000, now);
      const noiseGain = ctx.createGain();
      noiseGain.gain.setValueAtTime(0.08, now);
      noiseGain.gain.exponentialRampToValueAtTime(0.005, now + 0.015);
      noise.connect(filter);
      filter.connect(noiseGain);
      noiseGain.connect(ctx.destination);
      
      const osc = ctx.createOscillator();
      const oscGain = ctx.createGain();
      osc.type = "triangle";
      osc.frequency.setValueAtTime(350, now);
      osc.frequency.exponentialRampToValueAtTime(150, now + 0.03);
      oscGain.gain.setValueAtTime(0.12, now);
      oscGain.gain.exponentialRampToValueAtTime(0.005, now + 0.04);
      osc.connect(oscGain);
      oscGain.connect(ctx.destination);
      
      noise.start(now);
      noise.stop(now + 0.02);
      osc.start(now);
      osc.stop(now + 0.045);
      
    } else if (selectedSoundTheme.value === "bubble") {
      // Tono de burbuja ascendente
      const osc = ctx.createOscillator();
      const gain = ctx.createGain();
      osc.type = "sine";
      osc.frequency.setValueAtTime(900, now);
      osc.frequency.exponentialRampToValueAtTime(1400, now + 0.03);
      gain.gain.setValueAtTime(0.08, now);
      gain.gain.exponentialRampToValueAtTime(0.005, now + 0.03);
      osc.connect(gain);
      gain.connect(ctx.destination);
      osc.start(now);
      osc.stop(now + 0.035);
      
    } else if (selectedSoundTheme.value === "retro") {
      // Beep retro de 8 bits
      const osc = ctx.createOscillator();
      const gain = ctx.createGain();
      osc.type = "square";
      osc.frequency.setValueAtTime(600, now);
      gain.gain.setValueAtTime(0.04, now);
      gain.gain.exponentialRampToValueAtTime(0.001, now + 0.05);
      osc.connect(gain);
      gain.connect(ctx.destination);
      osc.start(now);
      osc.stop(now + 0.05);
    }
  } catch (e) {
    console.warn("Audio no soportado o bloqueado:", e);
  }
}

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

function cleanSongLine(line: string): string {
  let cleaned = line;

  // Limpiar expresiones comunes entre paréntesis de máximo 2-3 palabras cortas y vacías (como interjecciones)
  // Coincide con cosas como: (hey), (oh), (yeah), (huh), (ooh), (ah), (whoa), (wow), (baby), (hey hey), (oh oh), (yeah yeah), (mmm), etc.
  const fillerRegex =
    /\s*\((hey|oh|yeah|huh|ooh|ah|whoa|wow|baby|mmm|eh|vocals|instrumental|vocals\s+only|chuckle|gasp|sigh|huh\?|sí|no|oh-oh|yeah\s+yeah|hey\s+hey|oh\s+oh)\)\s*/gi;
  cleaned = cleaned.replace(fillerRegex, " ").trim();

  // Limpiar texto que representa interjecciones solas
  const singleFillerRegex =
    /^(hey|oh|yeah|huh|ooh|ah|whoa|wow|mmm|eh|vocals|instrumental|vocals\s+only)$/i;
  const normalizedForCheck = cleaned
    .replace(/[.,\/#!$%\^&\*;:{}=\-_`~()?¿¡]/g, "")
    .trim();
  if (singleFillerRegex.test(normalizedForCheck)) {
    return ""; // Omitir línea
  }

  cleaned = cleaned.replace(/\s+/g, " ").trim();
  return cleaned;
}

function processAndCleanPhrases(
  phrases: PhraseTranslation[]
): PhraseTranslation[] {
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

    // Si la frase quedó vacía tras la limpieza, se omite
    if (!englishText || englishText.length < 2) {
      continue;
    }

    if (skipDuplicateLines.value) {
      // Normalizar para comparación (minúsculas, sin puntuación básica ni espacios extra)
      const normText = englishText
        .toLowerCase()
        .replace(/[.,\/#!$%\^&\*;:{}=\-_`~()?¿¡]/g, "")
        .replace(/\s+/g, " ")
        .trim();
      if (seenTexts.has(normText)) {
        continue; // Omitir duplicados
      }
      seenTexts.add(normText);
    }

    const finalTranslations: Record<string, string> = {
      en: englishText,
    };
    if (spanishText) {
      finalTranslations.es = spanishText;
    } else {
      finalTranslations.es = englishText;
    }

    finalPhrases.push({
      ...p,
      text: englishText,
      translations: finalTranslations,
    });
  }

  if (finalPhrases.length === 0 && phrases.length > 0) {
    return phrases.filter((p) => p.text && p.text.trim().length > 0);
  }

  return finalPhrases;
}

const textareaPlaceholder = computed(() => {
  if (contentType.value === "song_translated") {
    return "Pega la letra alternada de Letras.com aquí:\n\nYou know you love me, I know you care\nTú sabes que me amas, sé que te importo\nJust shout whenever and I'll be there\nSolo grita cuando sea y estaré allí";
  }
  if (contentType.value === "song") {
    return "Pega la letra en inglés aquí. Cada línea será una frase para practicar:\n\nYou know you love me, I know you care\nJust shout whenever and I'll be there";
  }
  if (contentType.value === "dialogue") {
    return "Pega un diálogo o entrevista en inglés. Se detectarán los hablantes automáticamente:\n\nInterviewer: What is your name?\nCandidate: My name is John.";
  }
  return "Pega las oraciones, historia o párrafo en inglés aquí. Cada salto de línea será una oración para practicar:\n\nMy name is Lina.\nI live in London now.";
});

watch(newLessonText, (newText) => {
  if (!newText) {
    contentType.value = "text";
    return;
  }
  const lines = newText
    .split("\n")
    .map((l) => l.trim())
    .filter((l) => {
      if (l.startsWith("[") && l.endsWith("]")) return false;
      if (l.startsWith("(") && l.endsWith(")")) return false;
      return l.length > 0;
    });

  if (lines.length >= 2) {
    const enWords = [
      "the",
      "and",
      "to",
      "you",
      "of",
      "is",
      "that",
      "it",
      "in",
      "my",
      "me",
    ];
    const esWords = [
      "el",
      "la",
      "los",
      "las",
      "un",
      "una",
      "y",
      "que",
      "es",
      "en",
      "con",
      "mi",
      "se",
    ];

    let oddEnScore = 0;
    let oddEsScore = 0;
    let evenEnScore = 0;
    let evenEsScore = 0;

    for (let i = 0; i < Math.min(lines.length, 10); i++) {
      const words = lines[i].toLowerCase().split(/\s+/);
      words.forEach((w) => {
        const cleanW = w.replace(/[.,\/#!$%\^&\*;:{}=\-_`~()?]/g, "");
        if (enWords.includes(cleanW)) {
          if (i % 2 === 0) oddEnScore++;
          else evenEnScore++;
        }
        if (esWords.includes(cleanW)) {
          if (i % 2 === 0) oddEsScore++;
          else evenEsScore++;
        }
      });
    }

    // Si las líneas impares (índice 0, 2...) tienen más inglés y las pares (1, 3...) tienen más español
    if (oddEnScore > oddEsScore && evenEsScore > evenEnScore) {
      contentType.value = "song_translated";
    }
  }
});

function loadCustomLessons() {
  const stored = localStorage.getItem("lbl_custom_lessons");
  if (stored) {
    try {
      customLessons.value = JSON.parse(stored);
    } catch (e) {}
  }
}

function saveCustomLessons() {
  localStorage.setItem(
    "lbl_custom_lessons",
    JSON.stringify(customLessons.value)
  );
}

function deleteCustomLesson(id: string) {
  if (
    confirm("¿Estás seguro de que deseas eliminar esta lección personalizada?")
  ) {
    delete customLessons.value[id];
    saveCustomLessons();
    // Si la lección que se elimina es la que está activa, regresar a la lección inicial
    if (activeLessonId.value === id) {
      activeLessonId.value = "new_friend";
      handleLessonChange();
    }
  }
}

// Options for dropdown
const lessonOptions = computed(() => {
  const lang = profile.value.language;
  const list = Object.keys(lessonsData).map((key) => ({
    value: key,
    label: lessonsData[key].title[lang] || lessonsData[key].title["es"],
  }));

  // Append user's custom lessons
  Object.keys(customLessons.value).forEach((key) => {
    const lesson = customLessons.value[key];
    list.push({
      value: key,
      label:
        lesson.title[lang] ||
        lesson.title["es"] ||
        lesson.title["en"] ||
        "Lección sin título",
    });
  });

  // Append Create Lesson option
  list.push({
    value: "create_new_lesson",
    label:
      lang === "en" ? "+ Create new lesson..." : "+ Crear nueva lección...",
  });

  // Append Custom Practice option
  list.push({
    value: "custom_practice",
    label: lang === "en" ? "Custom Practice" : "Práctica personalizada",
  });

  return list;
});

// Active lesson label computed
const activeLessonLabel = computed(() => {
  const id = activeLessonId.value;
  const option = lessonOptions.value.find((opt) => opt.value === id);
  return option ? option.label : "";
});

function selectLesson(value: string) {
  activeLessonId.value = value;
  isDropdownOpen.value = false;
  handleLessonChange();
}

function cancelCreateLesson() {
  activeLessonId.value = "new_friend";
  handleLessonChange();
}

function createLessonSimple() {
  errorMessage.value = "";
  if (!newLessonTitle.value || !newLessonText.value) return;

  const rawLines = newLessonText.value
    .split("\n")
    .map((l) => l.trim())
    .filter((l) => {
      // Filtrar anotaciones comunes en corchetes o paréntesis
      if (l.startsWith("[") && l.endsWith("]")) return false;
      if (l.startsWith("(") && l.endsWith(")")) return false;
      return l.length > 0;
    });

  if (rawLines.length === 0) {
    errorMessage.value = "El texto de la lección está vacío.";
    return;
  }

  const lessonId = `custom_${Date.now()}`;
  let finalPhrases: PhraseTranslation[] = [];

  if (contentType.value === "song_translated") {
    // Agrupar de dos en dos (Inglés / Español) sin IA
    for (let i = 0; i < rawLines.length; i += 2) {
      const english = rawLines[i];
      const spanish = rawLines[i + 1] || english;
      finalPhrases.push({
        id: `cphrase_${lessonId}_${i / 2}`,
        text: english,
        translations: {
          es: spanish,
          en: english,
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

  // Filtrar duplicados y limpiar palabras vacías en base a la configuración seleccionada
  finalPhrases = processAndCleanPhrases(finalPhrases);

  const newLesson: Lesson = {
    title: {
      es: newLessonTitle.value,
      en: newLessonTitle.value,
    },
    phrases: finalPhrases,
  };

  customLessons.value[lessonId] = newLesson;
  saveCustomLessons();

  newLessonTitle.value = "";
  newLessonText.value = "";
  contentType.value = "text";

  activeLessonId.value = lessonId;
  handleLessonChange();
}

async function createLessonWithIA() {
  errorMessage.value = "";
  if (!newLessonTitle.value || !newLessonText.value) return;
  if (!geminiApiKey.value) {
    errorMessage.value =
      "Por favor, ingresa tu Gemini API Key en los ajustes de voz.";
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
      // Standard text/article
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

    const ai = new GoogleGenAI({ apiKey: geminiApiKey.value });
    const response = await ai.models.generateContent({
      model: selectedAiModel.value,
      contents: promptText,
      config: {
        responseMimeType: "application/json",
        tools: [{ googleSearch: {} }],
      },
    });

    const responseText = response.text;
    if (!responseText) {
      throw new Error("No se obtuvo respuesta de Gemini.");
    }

    const parsedJson = JSON.parse(responseText.trim());
    const lessonId = `custom_${Date.now()}`;

    // Validar y estructurar las frases devueltas
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

    // Filtrar duplicados y limpiar palabras vacías en base a la configuración seleccionada
    finalPhrases = processAndCleanPhrases(finalPhrases);

    if (finalPhrases.length === 0) {
      throw new Error(
        "No se pudieron extraer oraciones en inglés válidas del texto tras la limpieza."
      );
    }

    const newLesson: Lesson = {
      title: {
        es: parsedJson.title?.es || newLessonTitle.value,
        en: parsedJson.title?.en || newLessonTitle.value,
      },
      phrases: finalPhrases,
    };

    customLessons.value[lessonId] = newLesson;
    saveCustomLessons();

    newLessonTitle.value = "";
    newLessonText.value = "";
    contentType.value = "text";

    activeLessonId.value = lessonId;
    handleLessonChange();
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
  if (!geminiApiKey.value) {
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
    "es": "Título de la lección en español (ej: En la cafetería)",
    "en": "Título de la lección en inglés (ej: At the Coffee Shop)"
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

    const ai = new GoogleGenAI({ apiKey: geminiApiKey.value });
    const response = await ai.models.generateContent({
      model: selectedAiModel.value,
      contents: promptText,
      config: {
        responseMimeType: "application/json",
        tools: [{ googleSearch: {} }],
      },
    });

    const responseText = response.text;
    if (!responseText) {
      throw new Error("No se obtuvo respuesta de Gemini.");
    }

    const parsedJson = JSON.parse(responseText.trim());
    const lessonId = `custom_${Date.now()}`;

    // Validar y estructurar las frases devueltas
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

    // Aplicar optimizaciones si están configuradas
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

    customLessons.value[lessonId] = newLesson;
    saveCustomLessons();

    expressTopic.value = "";
    activeLessonId.value = lessonId;
    handleLessonChange();
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
    encodeURIComponent(JSON.stringify(customLessons.value, null, 2));
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

      // Fusionar con las lecciones existentes
      customLessons.value = {
        ...customLessons.value,
        ...parsed,
      };
      saveCustomLessons();
      target.value = "";
    } catch (err: any) {
      errorMessage.value = `Error al importar: ${err.message || err}`;
    }
  };
  reader.readAsText(file);
}

// Función para normalizar texto, removiendo caracteres Unicode invisibles y unificando apóstrofes/comillas
function normalizeText(text: string, isUserInput = false): string {
  if (!text) return "";
  let result = text
    .replace(/[\u200B-\u200D\uFEFF]/g, "") // Remover zero-width spaces y BOM
    .replace(/\u00A0/g, " ") // Normalizar espacios de no ruptura a espacios comunes
    .replace(/[’‘`´]/g, "'") // Normalizar apóstrofes y acentos a comilla simple
    .replace(/[“”]/g, '"') // Normalizar comillas dobles
    .replace(/[—–]/g, "-") // Normalizar guiones largos a guion común
    .replace(/…/g, "..."); // Normalizar puntos suspensivos

  if (isUserInput) {
    // El input del usuario no debe iniciar con espacios
    result = result.trimStart();
    // Reemplazar espacios múltiples consecutivos por uno solo
    result = result.replace(/\s{2,}/g, " ");
  } else {
    result = result.replace(/\s+/g, " ").trim();
  }
  return result;
}

// Compute Phrases of the Active Lesson and strip dot ending (rule requirement)
const activeLessonPhrases = computed(() => {
  let phrases: PhraseTranslation[] = [];
  const id = activeLessonId.value;

  if (lessonsData[id]) {
    phrases = [...lessonsData[id].phrases];
  } else if (customLessons.value[id]) {
    phrases = [...customLessons.value[id].phrases];
  } else if (id === "custom_practice") {
    const stored = localStorage.getItem("speak_phrases");
    if (stored) {
      try {
        const parsed = JSON.parse(stored);
        phrases = parsed.map((p: any, idx: number) => ({
          id: `cp-${idx}`,
          text: p.text,
          translations: {
            es: "Práctica personalizada",
            ar: "ممارسة مخصصة",
            pt: "Prática personalizada",
            fr: "Pratique personnalisée",
            de: "Benutzerdefinierte Praxis",
            it: "Pratica personalizzata",
            en: "Custom practice",
          },
        }));
      } catch (e) {}
    }
  }

  // Remove final period if exists (Rule 1: "no incluyan el punto") y normalizar
  return phrases.map((p) => {
    let cleanText = p.text.trim();
    if (cleanText.endsWith(".")) {
      cleanText = cleanText.slice(0, -1);
    }
    cleanText = normalizeText(cleanText, false);

    // Detectar prefijo del hablante (ej. "Interviewer:", "Candidate:", "Alex:")
    let speakerPrefix = "";
    const prefixMatch = cleanText.match(/^([A-Za-z0-9\s_-]+):\s*(.*)$/);
    if (prefixMatch) {
      speakerPrefix = prefixMatch[1] + ":";
      cleanText = prefixMatch[2].trim();
    }

    return {
      ...p,
      text: cleanText,
      speakerPrefix: speakerPrefix || undefined,
    };
  });
});

// Active phrase
const activePhrase = computed<PhraseTranslation | null>(() => {
  const phrases = activeLessonPhrases.value;
  if (phrases.length === 0 || activePhraseIndex.value >= phrases.length) {
    return null;
  }
  return phrases[activePhraseIndex.value];
});

// Computed active translation
const activeTranslation = computed(() => {
  const p = activePhrase.value;
  if (!p) return "";
  
  if (isBlindMode.value && !showBlindHint.value) {
    return "";
  }

  const lang = profile.value.language;
  let trans = p.translations[lang] || p.translations["es"] || "";

  if (p.speakerPrefix) {
    trans = trans.replace(/^([A-Za-z0-9\s_-]+):\s*(.*)$/, "$2");
  }
  return trans;
});

// Helper functions for Dictionary Lookup with Hover, Debounce and Cache
async function lookupWord(word: string, contextPhrase: string) {
  const cleanWord = word.replace(/[.,\/#!$%\^&\*;:{}=\-_`~()?]/g, "").trim();
  if (!cleanWord || cleanWord.length <= 1) return;

  const cacheKey = cleanWord.toLowerCase();

  // 1. Si está en caché, mostrar inmediatamente
  if (definitionsCache.value[cacheKey]) {
    activeWordDefinition.value = definitionsCache.value[cacheKey];
    return;
  }

  // 2. Si es la palabra clave (keyword) de la frase activa y coincide
  const p = activePhrase.value;
  if (
    p &&
    p.keyword &&
    p.keyword.toLowerCase() === cacheKey &&
    p.keywordTranslations
  ) {
    const keywordDef: WordDefinition = {
      word: cleanWord,
      partOfSpeech: "keyword",
      definition: "Palabra clave de esta frase para aprender.",
      translation: p.keywordTranslations.join(", "),
    };
    definitionsCache.value[cacheKey] = keywordDef;
    activeWordDefinition.value = keywordDef;
    return;
  }

  activeWordDefinition.value = {
    word: cleanWord,
    partOfSpeech: "",
    definition: "Buscando...",
    translation: "",
  };
  isLoadingDefinition.value = true;

  try {
    let partOfSpeech = "";
    let definition = "";
    let phonetic = "";
    let audioUrl = "";
    let translation = "";

    // A. Consultar la API del diccionario en inglés gratuita (siempre para fonética y audio)
    try {
      const res = await fetch(
        `https://api.dictionaryapi.dev/api/v2/entries/en/${cacheKey}`
      );
      if (res.ok) {
        const data = await res.json();
        if (Array.isArray(data) && data.length > 0) {
          const entry = data[0];
          const meaning = entry.meanings?.[0];
          partOfSpeech = meaning?.partOfSpeech || "";
          definition = meaning?.definitions?.[0]?.definition || "";
          phonetic =
            entry.phonetic ||
            entry.phonetics?.find((pt: any) => pt.text)?.text ||
            "";

          // Buscar el primer audio no vacío en el array de fonética
          const audioPart = entry.phonetics?.find(
            (pt: any) => pt.audio && pt.audio.trim() !== ""
          );
          if (audioPart) {
            audioUrl = audioPart.audio;
            if (audioUrl.startsWith("//")) {
              audioUrl = "https:" + audioUrl;
            }
          }
        }
      }
    } catch (e) {
      console.warn("Error al consultar dictionaryapi.dev:", e);
    }

    // B. Consultar Gemini para la traducción contextual en español
    if (geminiApiKey.value) {
      try {
        const promptText = `
Eres un diccionario bilingüe contextual inglés-español. Analiza la palabra en inglés "${cleanWord}" en el contexto de la frase "${contextPhrase}".
Genera un objeto JSON estructurado con el siguiente formato exacto:
{
  "translation": "traducción más exacta de la palabra al español en este contexto",
  "definition": "definición en inglés muy corta y simple si no la tienes (máximo 12 palabras)",
  "partOfSpeech": "noun, verb, adjective, adverb"
}
Retorna únicamente el JSON válido.
`;
        const ai = new GoogleGenAI({ apiKey: geminiApiKey.value });
        const response = await ai.models.generateContent({
          model: selectedAiModel.value,
          contents: promptText,
          config: {
            responseMimeType: "application/json",
          },
        });

        const responseText = response.text;
        if (responseText) {
          const parsed = JSON.parse(responseText.trim());
          translation = parsed.translation || "";
          if (!partOfSpeech) partOfSpeech = parsed.partOfSpeech || "";
          if (!definition) definition = parsed.definition || "";
        }
      } catch (err) {
        console.warn("Error al consultar traducción en Gemini:", err);
      }
    }

    // Valores por defecto de seguridad
    if (!definition) {
      definition = "No definition found.";
    }
    if (!translation) {
      translation = geminiApiKey.value
        ? "No se pudo traducir"
        : "(Configura Gemini API Key para ver traducción al español)";
    }

    const newDef: WordDefinition = {
      word: cleanWord,
      partOfSpeech,
      definition,
      translation,
      phonetic,
      audioUrl,
    };

    definitionsCache.value[cacheKey] = newDef;
    activeWordDefinition.value = newDef;
  } catch (err) {
    console.error("Error global en lookupWord:", err);
    activeWordDefinition.value = {
      word: cleanWord,
      partOfSpeech: "error",
      definition: "Error consultando la definición.",
      translation: "",
    };
  } finally {
    isLoadingDefinition.value = false;
  }
}

let pronounceTimer: number | null = null;

function onWordHoverPronounce(wordText: string) {
  // Limpiar puntuaciones para la pronunciación limpia
  const cleanWord = wordText
    .replace(/[.,\/#!$%\^&\*;:{}=\-_`~()?]/g, "")
    .trim();
  if (!cleanWord || cleanWord.length <= 1) return;

  if (pronounceTimer) {
    clearTimeout(pronounceTimer);
    pronounceTimer = null;
  }

  // Debounce de 200ms para pronunciar la palabra con TTS
  pronounceTimer = window.setTimeout(() => {
    speakText(cleanWord);
  }, 200);
}

function onWordLeavePronounce() {
  if (pronounceTimer) {
    clearTimeout(pronounceTimer);
    pronounceTimer = null;
  }
}

function closeDictionary() {
  activeWordDefinition.value = null;
  isLoadingDefinition.value = false;
}

function playWordAudio(url: string) {
  if (!url) return;
  try {
    const audio = new Audio(url);
    audio.play().catch((err) => {
      console.error("Error playing word audio:", err);
    });
  } catch (e) {
    console.error("Error playing word audio:", e);
  }
}

// Compute letter spans grouped by words for interactive dictionary hover support
interface LetterObj {
  char: string;
  class: string;
}

interface WordSpan {
  wordText: string;
  letters: LetterObj[];
}

const wordSpans = computed<WordSpan[]>(() => {
  const p = activePhrase.value;
  if (!p) {
    return [
      {
        wordText: "",
        letters: [
          {
            char: "No hay frases en este tema.",
            class: "text-slate-400 dark:text-slate-650 text-sm font-semibold",
          },
        ],
      },
    ];
  }

  const target = p.text;
  const current = normalizeText(typedText.value, true);

  // Generar las letras individuales con sus clases
  const letters = target.split("").map((char, index) => {
    let klass = "char-default";
    let displayChar = char;

    if (index < current.length) {
      if (current[index].toLowerCase() === target[index].toLowerCase()) {
        klass = "char-correct";
        // En modo ciego mostramos la letra ya escrita de forma correcta
        displayChar = char;
      } else {
        klass = "char-incorrect";
        if (char === " ") {
          klass += " char-space-incorrect";
        }
        displayChar = char;
      }
    } else {
      if (isBlindMode.value) {
        // En modo ciego, ocultar el resto de caracteres manteniendo la letra real para el layout
        displayChar = char;
        klass += " opacity-0 select-none pointer-events-none";
      } else if (isListeningMode.value && char !== " ") {
        // En Modo Escucha, ocultar las letras no escritas usando un punto medio (excepto espacios)
        displayChar = "•";
        klass += " opacity-40";
      }
    }

    if (index === current.length) {
      klass += " typing-caret";
      if (isBlindMode.value) {
        // El caracter activo también permanece oculto para el modo ciego
        displayChar = char;
      }
    }

    return {
      char: displayChar,
      class: klass,
    };
  });

  // Agrupar las letras en palabras basándose en los espacios del target
  const result: WordSpan[] = [];
  let currentWordLetters: LetterObj[] = [];
  let currentWordText = "";

  for (let i = 0; i < target.length; i++) {
    const char = target[i];
    currentWordLetters.push(letters[i]);
    currentWordText += char;

    // Si es un espacio o es el último carácter, se cierra la palabra actual
    if (char === " " || i === target.length - 1) {
      result.push({
        wordText: currentWordText.trim(),
        letters: currentWordLetters,
      });
      currentWordLetters = [];
      currentWordText = "";
    }
  }

  return result;
});

// Keyword Highlight dynamically
const showKeywordHighlight = computed(() => {
  const p = activePhrase.value;
  if (!p) return false;

  // Rule requirement: only show for Spanish native language
  if (profile.value.language !== "es") return false;
  if (
    !p.keyword ||
    !p.keywordTranslations ||
    p.keywordTranslations.length === 0
  )
    return false;

  const target = p.text.toLowerCase();
  const keyword = p.keyword.toLowerCase();
  const startIndex = target.indexOf(keyword);

  if (startIndex === -1) return false;

  const endIndex = startIndex + keyword.length;
  const cursor = normalizeText(typedText.value, true).length;

  // Show only if cursor is writing within the keyword bounds
  return cursor >= startIndex && cursor < endIndex;
});

// Lesson Completion overlay visibility
const showCompleteOverlay = computed(() => {
  const phrases = activeLessonPhrases.value;
  return phrases.length > 0 && activePhraseIndex.value >= phrases.length;
});

// Computed final stats for overlay
const finalWpmComputed = ref(0);
const finalAccComputed = ref(100);

function fallbackSpeechSynthesis(text: string) {
  if (typeof window === "undefined" || !window.speechSynthesis) return;
  window.speechSynthesis.cancel();

  const utterance = new SpeechSynthesisUtterance(text);
  const voice = voices.value.find((v) => v.name === selectedVoiceName.value);
  if (voice) {
    utterance.voice = voice;
  }

  utterance.rate = voiceRate.value;
  utterance.pitch = voicePitch.value;
  utterance.volume = voiceVolume.value;
  window.speechSynthesis.speak(utterance);
}

// Sound and synthesis
function speakText(text: string) {
  if (activeGeminiAudio) {
    activeGeminiAudio.pause();
    activeGeminiAudio = null;
  }

  if (useGeminiTts.value && geminiApiKey.value) {
    speakTextWithGemini(text);
  } else {
    fallbackSpeechSynthesis(text);
  }
}

async function speakTextWithGemini(text: string) {
  const cacheKey = text.trim().toLowerCase();

  // 1. Si el audio está en caché, reproducir de inmediato sin llamar a la API
  if (audioCache.value[cacheKey]) {
    try {
      const audio = audioCache.value[cacheKey];
      activeGeminiAudio = audio;
      audio.currentTime = 0; // Reiniciar al principio
      audio.volume = voiceVolume.value;
      const playPromise = audio.play();
      if (playPromise !== undefined) {
        playPromise.catch((err) => {
          console.error("Error al reproducir audio desde caché:", err);
        });
      }
      return;
    } catch (err) {
      console.error("Error al reproducir audio de caché:", err);
    }
  }

  try {
    const ai = new GoogleGenAI({ apiKey: geminiApiKey.value });
    const response = await ai.models.generateContent({
      model: "gemini-2.0-flash", // Modelo estándar v1beta
      contents: `Recite the following text exactly as written. Do not add any extra comments or text. Text: "${text}"`,
      config: {
        responseModalities: ["AUDIO"],
        speechConfig: {
          voiceConfig: {
            prebuiltVoiceConfig: {
              voiceName: "Aoede", // Voz de alta calidad (Aoede)
            },
          },
        },
      },
    });

    const part = response.candidates?.[0]?.content?.parts?.[0];
    if (part?.inlineData?.data) {
      const base64Data = part.inlineData.data;
      const mimeType = part.inlineData.mimeType || "audio/wav";
      const audioUrl = `data:${mimeType};base64,${base64Data}`;

      const audio = new Audio(audioUrl);
      audio.volume = voiceVolume.value;

      // Guardar el objeto de Audio en la caché
      audioCache.value[cacheKey] = audio;

      activeGeminiAudio = audio;
      const playPromise = audio.play();
      if (playPromise !== undefined) {
        playPromise.catch((err) => {
          console.error("Error al reproducir nuevo audio:", err);
        });
      }
    } else {
      console.warn(
        "La respuesta de Gemini no contiene datos de audio inline. Usando fallback del navegador."
      );
      fallbackSpeechSynthesis(text);
    }
  } catch (e: any) {
    console.warn(
      "La API de Gemini no soporta audio nativo en esta clave (Usando fallback del navegador):",
      e.message || e
    );
    fallbackSpeechSynthesis(text);
  }
}

function replayAudio() {
  const p = activePhrase.value;
  if (p) {
    speakText(p.text);
  }
}

// Input control
function focusInput() {
  if (inputRef.value) {
    inputRef.value.focus();
  }
}

// Stats & Typing loop processing
function processInput() {
  const phrases = activeLessonPhrases.value;
  if (phrases.length === 0 || activePhraseIndex.value >= phrases.length) return;

  const target = phrases[activePhraseIndex.value].text;

  // Normalizar la entrada del usuario de manera exhaustiva para realizar los cálculos y comparaciones
  const rawInput = typedText.value || "";
  const current = normalizeText(rawInput, true);

  // Limitar los cálculos a la longitud máxima del target
  const currentCapped = current.length > target.length ? current.slice(0, target.length) : current;

  // Disparar sonido de tecleo al añadir un nuevo carácter en base al texto limitado
  if (currentCapped.length > lastTypedLength) {
    const lastCharIndex = currentCapped.length - 1;
    const targetChar = target[lastCharIndex];
    if (targetChar && currentCapped[lastCharIndex].toLowerCase() === targetChar.toLowerCase()) {
      playKeyPressSound("correct");
    } else {
      playKeyPressSound("incorrect");
    }
  }
  lastTypedLength = currentCapped.length;

  const currentCleaned = currentCapped.trimEnd();

  if (!isTimerRunning.value) {
    startTime.value = Date.now();
    isTimerRunning.value = true;
  }

  // Errors calculation
  let errors = 0;
  const attempted = currentCleaned.length;
  for (let i = 0; i < attempted; i++) {
    const targetChar = target[i];
    if (
      !targetChar ||
      currentCleaned[i].toLowerCase() !== targetChar.toLowerCase()
    ) {
      errors++;
    }
  }

  // Update live stats in real-time
  if (startTime.value) {
    const elapsed = (Date.now() - startTime.value) / 60000;
    if (elapsed > 0) {
      // WPM calculated on correct characters matching index positions
      let correctChars = 0;
      for (let i = 0; i < currentCleaned.length; i++) {
        const targetChar = target[i];
        if (
          targetChar &&
          currentCleaned[i].toLowerCase() === targetChar.toLowerCase()
        ) {
          correctChars++;
        }
      }
      const wpmVal = Math.round(correctChars / 5 / elapsed);
      liveWpm.value = wpmVal;
    }

    if (attempted > 0) {
      let correctCount = 0;
      for (let i = 0; i < currentCleaned.length; i++) {
        const targetChar = target[i];
        if (
          targetChar &&
          currentCleaned[i].toLowerCase() === targetChar.toLowerCase()
        ) {
          correctCount++;
        }
      }
      const accVal = Math.round((correctCount / attempted) * 100);
      liveAcc.value = accVal;
    }
  }

  // Errors increment count
  if (errors > 0) {
    errorsCount.value += 1;
  }

  // Sentence match: case-insensitive check (Rule: Case-insensitive)
  if (currentCleaned.toLowerCase() === target.toLowerCase()) {
    playKeyPressSound("complete");
    lastTypedLength = 0;
    showBlindHint.value = false;

    completedPhrases.value.push({
      text: target,
      translation: activeTranslation.value,
      speakerPrefix: phrases[activePhraseIndex.value].speakerPrefix,
    });

    // Auto-scroll sidebar when item is added
    nextTick(() => {
      if (sidebarRef.value) {
        sidebarRef.value.scrollTop = sidebarRef.value.scrollHeight;
      }
    });

    activePhraseIndex.value++;
    typedText.value = "";
    if (inputRef.value) {
      inputRef.value.value = "";
    }

    // Trigger overlay or speak next phrase
    if (activePhraseIndex.value >= phrases.length) {
      // Calculate final stats
      const elapsedMinutes = (Date.now() - startTime.value) / 60000;
      let totalChars = 0;
      phrases.forEach((p) => (totalChars += p.text.length));

      const finalWpm = Math.round(totalChars / 5 / (elapsedMinutes || 1));
      let finalAcc = 100;
      if (errorsCount.value > 0) {
        finalAcc = Math.max(
          50,
          Math.round(((totalChars - errorsCount.value) / totalChars) * 100)
        );
      }

      finalWpmComputed.value = finalWpm;
      finalAccComputed.value = finalAcc;
    } else {
      setTimeout(() => {
        const nextP = activePhrase.value;
        if (nextP) {
          speakText(nextP.text);
        }
      }, 400);
    }
  }
}

function prevPhrase() {
  if (activePhraseIndex.value > 0) {
    activePhraseIndex.value--;
    typedText.value = "";
    lastTypedLength = 0;
    showBlindHint.value = false;
    if (inputRef.value) {
      inputRef.value.value = "";
    }
    // Hablar la frase anterior
    setTimeout(() => {
      const p = activePhrase.value;
      if (p) {
        speakText(p.text);
      }
    }, 200);
  }
}

function skipPhrase() {
  const phrases = activeLessonPhrases.value;
  if (phrases.length === 0) return;

  if (activePhraseIndex.value < phrases.length) {
    // Registrar en completados como omitida
    completedPhrases.value.push({
      text: phrases[activePhraseIndex.value].text,
      translation: (activeTranslation.value || "Omitida") + " (Omitida)",
      speakerPrefix: phrases[activePhraseIndex.value].speakerPrefix,
    });

    nextTick(() => {
      if (sidebarRef.value) {
        sidebarRef.value.scrollTop = sidebarRef.value.scrollHeight;
      }
    });

    activePhraseIndex.value++;
    typedText.value = "";
    lastTypedLength = 0;
    showBlindHint.value = false;
    if (inputRef.value) {
      inputRef.value.value = "";
    }

    if (activePhraseIndex.value >= phrases.length) {
      // Calcular estadísticas finales
      const elapsedMinutes = (Date.now() - startTime.value) / 60000;
      let totalChars = 0;
      phrases.forEach((p) => (totalChars += p.text.length));

      const finalWpm = Math.round(totalChars / 5 / (elapsedMinutes || 1));
      let finalAcc = 100;
      if (errorsCount.value > 0) {
        finalAcc = Math.max(
          50,
          Math.round(((totalChars - errorsCount.value) / totalChars) * 100)
        );
      }

      finalWpmComputed.value = finalWpm;
      finalAccComputed.value = finalAcc;
    } else {
      setTimeout(() => {
        const p = activePhrase.value;
        if (p) {
          speakText(p.text);
        }
      }, 200);
    }
  }
}

// Reset session variables
function resetSession() {
  typedText.value = "";
  errorsCount.value = 0;
  startTime.value = 0;
  isTimerRunning.value = false;
  liveWpm.value = 0;
  liveAcc.value = 100;
  activeWordDefinition.value = null;
  isLoadingDefinition.value = false;
  if (inputRef.value) {
    inputRef.value.value = "";
  }
}

function loadLessonData(lessonId: string) {
  activeLessonId.value = lessonId;
  activePhraseIndex.value = 0;
  completedPhrases.value = [];
  resetSession();

  if (lessonId === "create_new_lesson") return;

  nextTick(() => {
    const p = activePhrase.value;
    if (p) {
      speakText(p.text);
    }
    focusInput();
  });
}

function handleLessonChange() {
  loadLessonData(activeLessonId.value);
}

function restartLesson() {
  loadLessonData(activeLessonId.value);
}

function nextLesson() {
  const ids = Object.keys(lessonsData);
  let idx = ids.indexOf(activeLessonId.value) + 1;
  if (idx >= ids.length) idx = 0;
  loadLessonData(ids[idx]);
}

// Shortcut keyboard and focus redirect handler
function handleKeyDown(e: KeyboardEvent) {
  const wrapper = document.getElementById("view-typing");
  if (!wrapper || wrapper.classList.contains("hidden")) return;
  if (activeLessonId.value === "create_new_lesson") return;

  // Atajo de audio
  if (e.key === "\\") {
    e.preventDefault();
    replayAudio();
    return;
  }

  // Redirigir el foco al input de mecanografía si se presiona una tecla imprimible
  // pero solo si el foco no está en otro elemento input/select
  if (
    document.activeElement !== inputRef.value &&
    !e.ctrlKey &&
    !e.altKey &&
    !e.metaKey
  ) {
    if (e.key.length === 1 || e.key === "Backspace") {
      focusInput();
    }
  }
}

// Global click handler to maintain typing focus on background clicks
function handleGlobalClick(e: MouseEvent) {
  const wrapper = document.getElementById("view-typing");
  if (!wrapper || wrapper.classList.contains("hidden")) return;

  const target = e.target as HTMLElement;

  // Cerrar el selector de lecciones si el clic es fuera de su contenedor
  const isDropdownClick = target.closest("#lesson-select-wrapper");
  if (!isDropdownClick) {
    isDropdownOpen.value = false;
  }

  // Cerrar el panel de configuración de voz si el clic es fuera de su contenedor
  const isVoiceSettingsClick = target.closest("#voice-settings-wrapper");
  if (!isVoiceSettingsClick) {
    isVoiceSettingsOpen.value = false;
  }

  // Cerrar el diccionario si el clic es fuera de su card y no es en una palabra interactiva
  const isWordSpanInteractiveClick = target.closest(".word-span-interactive");
  const isDictionaryCardClick = target.closest("#word-dictionary-card");
  if (!isWordSpanInteractiveClick && !isDictionaryCardClick) {
    closeDictionary();
  }

  if (activeLessonId.value === "create_new_lesson") return;

  // Evitar re-enfocar si se hace clic en elementos interactivos (botones, dropdowns, menú superior)
  const isInteractive =
    target.closest("button") ||
    target.closest("a") ||
    target.closest("select") ||
    target.closest("input") ||
    target.closest("textarea") ||
    target.closest("header");

  if (!isInteractive) {
    focusInput();
  }
}

// Global Event Listeners
function handleOnboardingComplete() {
  readLocalProfile();
  loadLessonData("new_friend");
}

function handleLangChanged() {
  readLocalProfile();
  // Reload current phrase speech to capture possible localized voice if configured
  replayAudio();
}

function handleCustomPhrasesChange() {
  if (activeLessonId.value === "custom_practice") {
    loadLessonData("custom_practice");
  }
}

// Lifecycle Hooks
onMounted(() => {
  readLocalProfile();
  loadCustomLessons();
  loadLessonData("new_friend");
  loadVoices();

  const storedSidebar = localStorage.getItem("lbl_show_sidebar");
  if (storedSidebar !== null) {
    showSidebar.value = storedSidebar === "true";
  }

  if (typeof window !== "undefined" && window.speechSynthesis) {
    window.speechSynthesis.onvoiceschanged = loadVoices;
  }

  window.addEventListener("keydown", handleKeyDown);
  window.addEventListener("click", handleGlobalClick);
  window.addEventListener(
    "onboarding-completed-start",
    handleOnboardingComplete
  );
  window.addEventListener("native-lang-changed-state", handleLangChanged);
  window.addEventListener(
    "custom-phrases-changed-state-forward",
    handleCustomPhrasesChange
  );
});

onUnmounted(() => {
  if (typeof window !== "undefined" && window.speechSynthesis) {
    window.speechSynthesis.onvoiceschanged = null;
  }

  window.removeEventListener("keydown", handleKeyDown);
  window.removeEventListener("click", handleGlobalClick);
  window.removeEventListener(
    "onboarding-completed-start",
    handleOnboardingComplete
  );
  window.removeEventListener("native-lang-changed-state", handleLangChanged);
  window.removeEventListener(
    "custom-phrases-changed-state-forward",
    handleCustomPhrasesChange
  );
});

// Watch to speak initial phrase on load
watch(activePhrase, (newVal, oldVal) => {
  if (newVal && !oldVal) {
    speakText(newVal.text);
  }
});
</script>

<style scoped>
/* Keyframes matching custom fade-in */
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

/* Vue transition classes para sidebar */
.sidebar-enter-active,
.sidebar-leave-active {
  transition:
    max-width 0.4s cubic-bezier(0.16, 1, 0.3, 1),
    transform 0.4s cubic-bezier(0.16, 1, 0.3, 1),
    opacity 0.4s cubic-bezier(0.16, 1, 0.3, 1),
    padding 0.4s cubic-bezier(0.16, 1, 0.3, 1),
    border-color 0.4s cubic-bezier(0.16, 1, 0.3, 1);
  max-width: 320px;
  overflow: hidden;
  opacity: 1;
}

@media (min-width: 768px) {
  .sidebar-enter-from,
  .sidebar-leave-to {
    opacity: 0;
    max-width: 0px !important;
    transform: translateX(-30px);
    padding-left: 0 !important;
    padding-right: 0 !important;
    border-right-width: 0px !important;
    border-color: transparent !important;
  }
}

@media (max-width: 767px) {
  .sidebar-enter-active,
  .sidebar-leave-active {
    transition:
      max-height 0.4s cubic-bezier(0.16, 1, 0.3, 1),
      transform 0.4s cubic-bezier(0.16, 1, 0.3, 1),
      opacity 0.4s cubic-bezier(0.16, 1, 0.3, 1),
      padding 0.4s cubic-bezier(0.16, 1, 0.3, 1);
    max-height: 200px;
    opacity: 1;
    overflow: hidden;
  }

  .sidebar-enter-from,
  .sidebar-leave-to {
    opacity: 0;
    max-height: 0px !important;
    transform: translateY(-20px);
    padding-top: 0 !important;
    padding-bottom: 0 !important;
  }
}

/* Vue transition classes para dropdown */
.dropdown-enter-active,
.dropdown-leave-active {
  transition:
    opacity 0.15s ease,
    transform 0.15s ease;
}

.dropdown-enter-from,
.dropdown-leave-to {
  opacity: 0;
  transform: translateY(-8px);
}


/* Estilo para los errores de espacio (bloque vertical translúcido) */
.char-space-incorrect {
  background-color: rgba(239, 68, 68, 0.35) !important;
  display: inline-block;
  min-width: 0.6em;
  height: 1.10em;
  line-height: 1.10em;
  vertical-align: middle;
  border-radius: 3px;
}

/* ==========================================================================
   ESTILOS DE TEMAS VISUALES PREMIUM (Idea 5)
   ========================================================================== */

/* 1. TEMA CYBERPUNK (Neon Dark) */
#view-typing.theme-cyberpunk {
  --color-slate-50: #0d0517;
  --color-slate-100: #1b0a2c;
  --color-slate-200: #3c1460;
  --color-slate-350: #bf55ec;
  --color-slate-400: #542b87;
  --color-slate-500: #a562f5;
  --color-slate-600: #c091fb;
  --color-slate-700: #dbbffd;
  --color-slate-800: #2d104d;
  --color-slate-900: #f5efff;
  --color-slate-950: #06020b;
  
  --color-primary-50: #18032c;
  --color-primary-100: #320a56;
  --color-primary-200: #56168e;
  --color-primary-500: #ff007f;
  --color-primary-600: #00ffff;
  --color-primary-650: #00ffff;
  --color-primary-700: #00d5d5;
  
  background-color: #07020d !important;
  color: #f5efff !important;
}

#view-typing.theme-cyberpunk .char-default {
  color: #4f2979 !important;
}

#view-typing.theme-cyberpunk .char-correct {
  color: #00ffff !important;
  text-shadow: 0 0 8px rgba(0, 255, 255, 0.6);
}

#view-typing.theme-cyberpunk .char-incorrect {
  color: #ff007f !important;
  background-color: rgba(255, 0, 127, 0.15) !important;
  text-shadow: 0 0 8px rgba(255, 0, 127, 0.6);
}

#view-typing.theme-cyberpunk .typing-caret::after {
  background-color: #ff007f !important;
  box-shadow: 0 0 10px #ff007f, 0 0 20px #ff007f !important;
}

/* 2. TEMA MIDNIGHT FOREST (Sage & Mint) */
#view-typing.theme-forest {
  --color-slate-50: #0c1813;
  --color-slate-100: #12261d;
  --color-slate-200: #1a382a;
  --color-slate-350: #5eead4;
  --color-slate-400: #2a4738;
  --color-slate-500: #3c6a52;
  --color-slate-600: #4e8c6c;
  --color-slate-700: #60af86;
  --color-slate-800: #1a382a;
  --color-slate-900: #e6f5ee;
  --color-slate-950: #050c09;
  
  --color-primary-50: #0c1f16;
  --color-primary-100: #143324;
  --color-primary-200: #1e4d37;
  --color-primary-500: #5eead4;
  --color-primary-600: #fbbf24;
  --color-primary-650: #fbbf24;
  --color-primary-700: #d97706;

  background-color: #080f0c !important;
  color: #e6f5ee !important;
}

#view-typing.theme-forest .char-default {
  color: #274435 !important;
}

#view-typing.theme-forest .char-correct {
  color: #5eead4 !important;
  text-shadow: 0 0 4px rgba(94, 234, 212, 0.3);
}

#view-typing.theme-forest .char-incorrect {
  color: #f87171 !important;
  background-color: rgba(248, 113, 113, 0.12) !important;
}

#view-typing.theme-forest .typing-caret::after {
  background-color: #fbbf24 !important;
  box-shadow: 0 0 8px #fbbf24 !important;
}

/* 3. TEMA SAKURA BLOSSOM (Cherry Pink) */
#view-typing.theme-sakura {
  --color-slate-50: #fff0f2;
  --color-slate-100: #ffe3e7;
  --color-slate-200: #ffd1d8;
  --color-slate-350: #ff5c8a;
  --color-slate-400: #d0b2b9;
  --color-slate-550: #7a515a;
  --color-slate-600: #9c6b76;
  --color-slate-700: #bd8691;
  --color-slate-800: #ffd1d8;
  --color-slate-900: #4a2b31;
  --color-slate-950: #fff5f6;
  
  --color-primary-50: #fff0f2;
  --color-primary-100: #ffe3e7;
  --color-primary-200: #ffd1d8;
  --color-primary-500: #ff5c8a;
  --color-primary-600: #e11d48;
  --color-primary-650: #be123c;
  --color-primary-700: #be123c;

  background-color: #fff9fa !important;
  color: #4a2b31 !important;
}

#view-typing.theme-sakura .char-default {
  color: #d8bdc3 !important;
}

#view-typing.theme-sakura .char-correct {
  color: #ff5c8a !important;
  text-shadow: 0 0 4px rgba(255, 92, 138, 0.2);
}

#view-typing.theme-sakura .char-incorrect {
  color: #e11d48 !important;
  background-color: rgba(225, 29, 72, 0.1) !important;
}

#view-typing.theme-sakura .typing-caret::after {
  background-color: #ff5c8a !important;
  box-shadow: 0 0 6px #ff5c8a !important;
}

/* Sakura Blossom Dark Mode overrides */
.dark #view-typing.theme-sakura {
  --color-slate-50: #291519;
  --color-slate-100: #3b1e24;
  --color-slate-200: #5b2f38;
  --color-slate-350: #ff85a1;
  --color-slate-400: #6a444d;
  --color-slate-500: #9c606e;
  --color-slate-600: #c98292;
  --color-slate-700: #f5a0b2;
  --color-slate-800: #5b2f38;
  --color-slate-900: #ffeef0;
  --color-slate-950: #1b0d10;
  
  --color-primary-50: #2d1318;
  --color-primary-100: #451b24;
  --color-primary-200: #692a37;
  --color-primary-500: #ff85a1;
  --color-primary-600: #ff5c8a;
  --color-primary-650: #ff5c8a;
  --color-primary-700: #e11d48;

  background-color: #190b0e !important;
  color: #ffeef0 !important;
}

.dark #view-typing.theme-sakura .char-default {
  color: #5c3b42 !important;
}

.dark #view-typing.theme-sakura .char-correct {
  color: #ff85a1 !important;
  text-shadow: 0 0 6px rgba(255, 133, 161, 0.3);
}

.dark #view-typing.theme-sakura .char-incorrect {
  color: #ff5c8a !important;
  background-color: rgba(255, 92, 138, 0.15) !important;
}

.dark #view-typing.theme-sakura .typing-caret::after {
  background-color: #ff85a1 !important;
  box-shadow: 0 0 8px #ff85a1 !important;
}
</style>
