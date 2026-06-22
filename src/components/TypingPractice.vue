<template>
  <div
    id="view-typing"
    :class="'theme-' + selectedTheme"
    class="grow flex flex-col md:flex-row overflow-hidden relative transition-colors duration-300"
  >
    <!-- LEFT SIDEBAR (COMPLETED LIST) -->
    <LessonsSidebar
      :show-sidebar="showSidebar"
      :active-lesson-id="activeLessonId"
      :completed-lessons="completedLessons"
      :custom-lessons="customLessons"
      :difficult-phrases="difficultPhrases"
      :profile="profile"
      @toggle-sidebar="toggleSidebar"
      @select-lesson="selectLesson"
      @delete-custom-lesson="deleteCustomLesson"
      @clear-difficult-phrases="clearDifficultPhrases"
    />

    <!-- CENTRAL TYPING AREA -->
    <section
      class="grow flex flex-col justify-between p-6 md:px-12 md:pb-12 md:pt-6 items-center relative overflow-hidden select-none"
    >
      <!-- Lesson Info / Topic Selector -->
      <div
        class="w-full max-w-6xl flex flex-wrap justify-between items-center gap-y-3 gap-x-4 border-b border-slate-200/60 dark:border-slate-800/30 pb-4 mb-4 select-none"
      >
        <!-- Toggle Sidebar Button (order-1) -->
        <button
          @click="toggleSidebar"
          class="p-2 rounded-xl border transition-all flex items-center justify-center cursor-pointer select-none gap-1.5 order-1"
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

        <!-- Topic Selector (order-3 in mobile, order-2 in desktop) -->
        <div
          class="flex items-center gap-2 justify-center md:justify-start w-full md:w-auto order-3 md:order-2 py-1.5 md:py-0 border-t border-slate-100 dark:border-slate-850/40 md:border-t-0 mt-1 md:mt-0"
          id="lesson-select-wrapper"
        >
          <span
            class="text-xs font-semibold text-slate-400 dark:text-slate-500 uppercase tracking-wider"
          >
            Tema:
          </span>
          <span
            class="text-sm font-extrabold text-primary-650 dark:text-primary-400 text-center select-text"
          >
            {{ activeLessonLabel }}
          </span>
        </div>

        <div class="flex items-center gap-1.5 order-2 md:order-3">
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
            <span>{{ showBlindHint ? "👁️" : "👁️‍🗨️" }}</span>
            <span class="hidden sm:inline">Pista</span>
          </button>

          <!-- Sonido de Teclas Rápido Toggle -->
          <button
            id="btn-sound-toggle"
            @click="toggleSoundMute"
            class="p-2 rounded-lg transition-colors flex items-center justify-center gap-1.5 text-xs font-bold cursor-pointer select-none border"
            :class="
              selectedSoundTheme !== 'none'
                ? 'bg-emerald-50 border-emerald-200/60 text-emerald-650 dark:bg-emerald-950/30 dark:border-emerald-900/20 dark:text-emerald-400 shadow-xs'
                : 'bg-slate-100 border-slate-200/50 text-slate-500 dark:bg-slate-900/30 dark:border-slate-800/30 dark:text-slate-400 hover:bg-slate-200/80 dark:hover:bg-slate-900/60'
            "
            :title="
              selectedSoundTheme !== 'none'
                ? 'Silenciar sonidos de teclas'
                : 'Activar sonidos de teclas'
            "
          >
            <span>{{ selectedSoundTheme !== "none" ? "🔊" : "🔇" }}</span>
            <span class="hidden sm:inline">Sonido</span>
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

            <VoiceSettingsDropdown
              :is-open="isVoiceSettingsOpen"
              :voices="voices"
              v-model:selected-voice-name="selectedVoiceName"
              v-model:voice-rate="voiceRate"
              v-model:voice-pitch="voicePitch"
              v-model:voice-volume="voiceVolume"
              v-model:use-gemini-tts="useGeminiTts"
              v-model:gemini-api-key="geminiApiKey"
              v-model:selected-sound-theme="selectedSoundTheme"
              v-model:selected-theme="selectedTheme"
              v-model:selected-ai-model="selectedAiModel"
            />
          </div>
        </div>
      </div>

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
            class="hidden md:flex p-3.5 rounded-full border border-slate-200/60 dark:border-slate-800/40 bg-white/70 dark:bg-slate-900/65 hover:bg-slate-100 dark:hover:bg-slate-850 text-slate-450 dark:text-slate-500 disabled:opacity-20 disabled:hover:bg-transparent dark:disabled:hover:bg-transparent transition-all cursor-pointer disabled:cursor-not-allowed select-none shrink-0"
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
          <!-- Barra de progreso visual sutil -->
          <div
            class="absolute top-0 left-0 right-0 h-1 bg-slate-300 dark:bg-slate-600 rounded-full overflow-hidden"
          >
            <div
              class="h-full bg-linear-to-r from-primary-500 to-primary-650 dark:from-primary-600 dark:to-primary-400 transition-all duration-300 ease-out"
              :style="{ width: `${progressPercent}%` }"
            ></div>
          </div>

          <!-- TARGET TYPING TEXT CONTAINER -->
          <div
            id="typing-box-container"
            @click="focusInput"
            class="grow flex flex-col justify-center items-left py-12 relative cursor-text min-h-[200px]"
          >
            <!-- HIDDEN INPUT FOR DRIVING THE KEYBOARD ON MOBILE/DESKTOP -->
            <input
              id="typing-hidden-input"
              type="text"
              ref="inputRef"
              :value="typedText"
              @input="handleInput"
              @focus="isFocused = true"
              @blur="isFocused = false"
              autocomplete="off"
              autocorrect="off"
              autocapitalize="none"
              spellcheck="false"
              inputmode="search"
              class="absolute top-0 left-0 w-full h-full opacity-0 -z-10 pointer-events-none"
            />
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
              class="text-3xl sm:text-3xl md:text-5xl lg:text-6xl font-bold tracking-normal font-mono leading-relaxed mb-4 select-none flex flex-wrap justify-left relative"
            >
              <!-- Smooth Caret Element -->
              <div
                v-if="caretStyle.opacity > 0"
                class="absolute h-[4px] bg-primary-600 dark:bg-primary-400 transition-all duration-100 ease-out pointer-events-none rounded-full smooth-caret-active"
                :style="caretStyle"
              ></div>
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
                  class="whitespace-pre"
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
            <WordLookupModal
              :definition="activeWordDefinition"
              :is-loading="isLoadingDefinition"
              @close="activeWordDefinition = null"
              @play-audio="playWordAudio"
              @speak-syllable="speakText"
            />
          </div>

          <!-- Botón frase siguiente (Omitir) -->
          <button
            @click="skipPhrase"
            class="hidden md:flex p-3.5 rounded-full border border-slate-200/60 dark:border-slate-800/40 bg-white/70 dark:bg-slate-900/65 hover:bg-slate-100 dark:hover:bg-slate-850 text-slate-450 dark:text-slate-500 transition-all cursor-pointer select-none shrink-0"
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

        <!-- Mobile phrase navigation controls (hidden on desktop) -->
        <div
          class="flex md:hidden items-center justify-center gap-6 mt-2 mb-6 select-none shrink-0 w-full"
        >
          <button
            @click="prevPhrase"
            :disabled="activePhraseIndex === 0"
            class="flex items-center justify-center gap-1.5 px-5 py-2.5 rounded-xl border border-slate-200 dark:border-slate-800 bg-white dark:bg-slate-900 text-slate-650 dark:text-slate-350 disabled:opacity-30 disabled:cursor-not-allowed transition-all text-xs font-bold cursor-pointer select-none active:scale-95 shadow-sm"
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
            <span>Anterior</span>
          </button>
          <button
            @click="skipPhrase"
            class="flex items-center justify-center gap-1.5 px-5 py-2.5 rounded-xl border border-slate-200 dark:border-slate-800 bg-white dark:bg-slate-900 text-slate-650 dark:text-slate-350 transition-all text-xs font-bold cursor-pointer select-none active:scale-95 shadow-sm"
          >
            <span>Omitir</span>
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
          class="absolute inset-0 bg-slate-50/98 dark:bg-slate-950/98 z-40 flex flex-col items-center justify-start sm:justify-center p-6 overflow-y-auto animate-fade-in"
        >
          <div
            class="glass p-8 my-auto rounded-3xl border border-white/20 dark:border-white/5 shadow-2xl max-w-md w-full text-center flex flex-col items-center gap-6 relative overflow-hidden shrink-0"
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

            <!-- Quiz results if completed -->
            <div
              v-if="quizQuestions.length > 0"
              class="w-full flex items-center justify-between p-3.5 rounded-2xl bg-indigo-50/50 dark:bg-indigo-950/20 border border-indigo-100 dark:border-indigo-900/40 text-xs text-indigo-750 dark:text-indigo-305 font-bold"
            >
              <div class="flex items-center gap-2">
                <span>🧠</span>
                <span>Quiz de Vocabulario:</span>
              </div>
              <span
                class="px-2 py-0.5 bg-indigo-100 dark:bg-indigo-900/60 rounded-lg text-indigo-850 dark:text-indigo-250 font-black"
              >
                {{ quizScore }} / {{ quizQuestions.length }} correctas
              </span>
            </div>

            <!-- New difficult phrases saved banner -->
            <div
              v-if="newDifficultCount > 0"
              class="w-full flex items-center justify-between p-3.5 rounded-2xl bg-amber-50/60 dark:bg-amber-950/25 border border-amber-200/50 dark:border-amber-900/30 text-xs text-amber-800 dark:text-amber-300 font-bold"
            >
              <div class="flex items-center gap-2 text-left">
                <span class="text-amber-500">🔄</span>
                <span>Guardadas para repaso:</span>
              </div>
              <span
                class="px-2 py-0.5 bg-amber-100 dark:bg-amber-900/60 rounded-lg text-amber-900 dark:text-amber-250 font-black"
              >
                {{ newDifficultCount }} frases
              </span>
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

        <!-- MINI VOCABULARY QUIZ OVERLAY -->
        <div
          v-if="showVocabQuiz && quizQuestions.length > 0"
          id="vocab-quiz-overlay"
          class="absolute inset-0 bg-slate-50/98 dark:bg-slate-950/98 z-40 flex flex-col items-center justify-start sm:justify-center p-6 overflow-y-auto animate-fade-in"
        >
          <div
            class="glass p-8 my-auto rounded-3xl border border-white/20 dark:border-white/5 shadow-2xl max-w-md w-full text-center flex flex-col items-center gap-6 relative overflow-hidden shrink-0"
          >
            <div
              class="absolute top-0 left-0 right-0 h-2 bg-gradient-to-r from-indigo-500 via-purple-500 to-pink-500"
            ></div>

            <!-- Quiz Header/Progress -->
            <div
              class="w-full flex justify-between items-center text-xs font-bold text-slate-400 dark:text-slate-500"
            >
              <span
                class="px-2.5 py-1 bg-indigo-50 dark:bg-indigo-950/40 text-indigo-650 dark:text-indigo-400 rounded-lg"
              >
                Pregunta {{ currentQuizIndex + 1 }} de
                {{ quizQuestions.length }}
              </span>
              <span>Score: {{ quizScore }}</span>
            </div>

            <!-- Quiz Question -->
            <div class="my-2">
              <span
                class="text-3xs uppercase tracking-widest font-black text-indigo-500"
                >¿Qué significa esta palabra?</span
              >
              <h3
                class="text-3xl font-black text-slate-800 dark:text-white mt-1 select-none"
              >
                {{ quizQuestions[currentQuizIndex].keyword }}
              </h3>
              <p
                class="text-3xs text-slate-450 dark:text-slate-505 mt-2 italic max-w-xs mx-auto border-t border-slate-100 dark:border-slate-850 pt-2"
              >
                "{{ quizQuestions[currentQuizIndex].phraseText }}"
              </p>
            </div>

            <!-- Quiz Options -->
            <div class="flex flex-col gap-3 w-full">
              <button
                v-for="(option, idx) in quizQuestions[currentQuizIndex].options"
                :key="idx"
                @click="answerQuiz(option)"
                :disabled="quizAnswered"
                class="w-full text-left py-3.5 px-4 rounded-xl border text-xs font-bold transition-all flex items-center justify-between cursor-pointer"
                :class="[
                  !quizAnswered
                    ? 'bg-white border-slate-200 text-slate-700 hover:border-indigo-300 hover:bg-indigo-50/20 dark:bg-slate-900 dark:border-slate-800 dark:text-slate-300 dark:hover:border-indigo-950 dark:hover:bg-indigo-950/10'
                    : option === quizQuestions[currentQuizIndex].correctAnswer
                      ? 'bg-emerald-500 border-emerald-500 text-white dark:bg-emerald-600 dark:border-emerald-600'
                      : option === quizSelectedOption
                        ? 'bg-rose-500 border-rose-500 text-white dark:bg-rose-600 dark:border-rose-600'
                        : 'bg-white border-slate-200 text-slate-400 dark:bg-slate-900 dark:border-slate-800 dark:text-slate-650 opacity-60',
                ]"
              >
                <span>{{ option }}</span>
                <span
                  v-if="
                    quizAnswered &&
                    option === quizQuestions[currentQuizIndex].correctAnswer
                  "
                  class="text-sm"
                  >✓</span
                >
                <span
                  v-if="
                    quizAnswered &&
                    option === quizSelectedOption &&
                    option !== quizQuestions[currentQuizIndex].correctAnswer
                  "
                  class="text-sm"
                  >✗</span
                >
              </button>
            </div>

            <!-- Skip button -->
            <button
              @click="skipQuiz"
              class="text-3xs text-slate-450 hover:text-slate-650 dark:text-slate-500 dark:hover:text-slate-400 font-bold transition-colors mt-2"
            >
              Saltar Quiz y ver estadísticas
            </button>
          </div>
        </div>
      </div>

      <!-- CREADOR DE LECCIONES PERSONALIZADAS -->
      <LessonCreator
        v-else
        :custom-lessons="customLessons"
        :gemini-api-key="geminiApiKey"
        :selected-ai-model="selectedAiModel"
        @lesson-created="handleCustomLessonCreated"
        @cancel="cancelCreateLesson"
      />
    </section>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, nextTick, onMounted, onUnmounted, watch } from "vue";
import { GoogleGenAI } from "@google/genai";
import LessonsSidebar from "./LessonsSidebar.vue";
import VoiceSettingsDropdown from "./VoiceSettingsDropdown.vue";
import LessonCreator from "./LessonCreator.vue";
import WordLookupModal from "./WordLookupModal.vue";

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
  level?: "beginner" | "intermediate" | "advanced";
  phrases: PhraseTranslation[];
}

import rawLessonsIndex from "../data/lessons_index.json";
const lessonsIndex = rawLessonsIndex as Array<{
  id: string;
  level: string;
  category?: string;
  title: Record<string, string>;
}>;

// Carga perezosa de lecciones usando import.meta.glob de Vite
const lessonsModules = import.meta.glob("../data/lessons/*.json");
const loadedLessonData = ref<Lesson | null>(null);
const completedLessons = ref<Record<string, boolean>>({});

const tLocalMap: Record<string, Record<string, string>> = {
  es: {
    congrats: "¡Lección Completada!",
    desc: "¡Excelente trabajo practicando tu inglés!",
    repeat: "para repetir el audio",
    topic: "Tema:",
  },
  en: {
    congrats: "Lesson Completed!",
    desc: "Great job practicing your English!",
    repeat: "to repeat audio",
    topic: "Topic:",
  },
};

// Reactivity states
const activeLessonId = ref("key_greetings");
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
  syllables?: string[];
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
const activeSidebarTab = ref("roadmap"); // 'roadmap' o 'custom'

// Agrupación de lecciones para la Ruta de Aprendizaje
const lessonsByLevel = computed(() => {
  const beginner = lessonsIndex.filter((l) => l.level === "beginner");
  const intermediate = lessonsIndex.filter((l) => l.level === "intermediate");
  const advanced = lessonsIndex.filter((l) => l.level === "advanced");
  return { beginner, intermediate, advanced };
});

// --- FEATURE 4: NIVELES DE DIFICULTAD (Badges) ---
function getLevelBadge(
  lessonId: string
): { emoji: string; label: string; classes: string } | null {
  const meta = lessonsIndex.find((l) => l.id === lessonId);
  const level = meta ? meta.level : customLessons.value[lessonId]?.level;
  if (!level) return null;
  if (level === "beginner")
    return {
      emoji: "🟢",
      label: "A1",
      classes:
        "bg-emerald-100 text-emerald-700 dark:bg-emerald-950/40 dark:text-emerald-400 border-emerald-200/60 dark:border-emerald-800/30",
    };
  if (level === "intermediate")
    return {
      emoji: "🟡",
      label: "B1",
      classes:
        "bg-amber-100 text-amber-700 dark:bg-amber-950/40 dark:text-amber-400 border-amber-200/60 dark:border-amber-800/30",
    };
  if (level === "advanced")
    return {
      emoji: "🔴",
      label: "C1",
      classes:
        "bg-rose-100 text-rose-700 dark:bg-rose-950/40 dark:text-rose-400 border-rose-200/60 dark:border-rose-800/30",
    };
  return null;
}

// --- FEATURE 1: SISTEMA DE REPASO ESPACIADO ---
interface DifficultPhrase {
  phrase: PhraseTranslation;
  lessonId: string;
  lessonTitle: string;
  accuracy: number;
  timestamp: number;
}

const difficultPhrases = ref<DifficultPhrase[]>([]);
const newDifficultCount = ref(0); // Frases difíciles detectadas en la sesión actual
const currentPhraseErrors = ref(0); // Cuenta de errores de la frase actual

function loadDifficultPhrases() {
  const stored = localStorage.getItem("lbl_difficult_phrases");
  if (stored) {
    try {
      difficultPhrases.value = JSON.parse(stored);
    } catch (e) {}
  }
}

function saveDifficultPhrases() {
  localStorage.setItem(
    "lbl_difficult_phrases",
    JSON.stringify(difficultPhrases.value)
  );
}

function addDifficultPhrase(
  phrase: PhraseTranslation,
  lessonId: string,
  accuracy: number
) {
  // Evitar duplicados (misma frase del mismo lesson)
  const exists = difficultPhrases.value.some(
    (dp) =>
      dp.phrase.text.toLowerCase() === phrase.text.toLowerCase() &&
      dp.lessonId === lessonId
  );
  if (exists) return;

  const meta = lessonsIndex.find((l) => l.id === lessonId);
  const lessonTitle =
    meta?.title?.[profile.value.language] ||
    meta?.title?.["es"] ||
    customLessons.value[lessonId]?.title?.[profile.value.language] ||
    customLessons.value[lessonId]?.title?.["es"] ||
    "Lección";

  difficultPhrases.value.push({
    phrase: { ...phrase },
    lessonId,
    lessonTitle,
    accuracy: Math.round(accuracy),
    timestamp: Date.now(),
  });
  newDifficultCount.value++;
  saveDifficultPhrases();
}

function removeDifficultPhrase(index: number) {
  difficultPhrases.value.splice(index, 1);
  saveDifficultPhrases();
}

function clearDifficultPhrases() {
  difficultPhrases.value = [];
  saveDifficultPhrases();
}

// --- FEATURE 3: MINI QUIZ DE VOCABULARIO ---
interface QuizQuestion {
  keyword: string;
  correctAnswer: string;
  options: string[];
  phraseText: string;
}

const showVocabQuiz = ref(false);
const quizQuestions = ref<QuizQuestion[]>([]);
const currentQuizIndex = ref(0);
const quizScore = ref(0);
const quizSelectedOption = ref<string | null>(null);
const quizAnswered = ref(false);
const quizFinished = ref(false);

function generateQuizQuestions() {
  const phrases = activeLessonPhrases.value;
  // Recopilar frases con keywords
  const withKeywords = phrases.filter(
    (p) =>
      p.keyword && p.keywordTranslations && p.keywordTranslations.length > 0
  );
  if (withKeywords.length < 2) return false;

  // Seleccionar hasta 4 al azar
  const shuffled = [...withKeywords].sort(() => Math.random() - 0.5);
  const selected = shuffled.slice(0, Math.min(4, shuffled.length));

  // Recopilar todas las traducciones disponibles para distractores
  const allTranslations = withKeywords
    .filter((p) => p.keywordTranslations)
    .map((p) => p.keywordTranslations![0])
    .filter(Boolean);

  quizQuestions.value = selected.map((phrase) => {
    const correctAnswer = phrase.keywordTranslations![0];
    // Generar 3 distractores (diferentes de la respuesta correcta)
    const distractors = allTranslations
      .filter((t) => t.toLowerCase() !== correctAnswer.toLowerCase())
      .sort(() => Math.random() - 0.5)
      .slice(0, 3);

    // Si no hay suficientes distractores, rellenar con genéricos
    while (distractors.length < 3) {
      const fallbacks = [
        "recordar",
        "comprar",
        "necesitar",
        "trabajar",
        "cocinar",
        "viajar",
        "estudiar",
        "hablar",
      ];
      const fallback = fallbacks[Math.floor(Math.random() * fallbacks.length)];
      if (!distractors.includes(fallback) && fallback !== correctAnswer) {
        distractors.push(fallback);
      }
    }

    // Mezclar opciones
    const options = [correctAnswer, ...distractors].sort(
      () => Math.random() - 0.5
    );

    return {
      keyword: phrase.keyword!,
      correctAnswer,
      options,
      phraseText: phrase.text,
    };
  });

  currentQuizIndex.value = 0;
  quizScore.value = 0;
  quizSelectedOption.value = null;
  quizAnswered.value = false;
  quizFinished.value = false;

  return true;
}

function answerQuiz(selectedOption: string) {
  if (quizAnswered.value) return;
  quizSelectedOption.value = selectedOption;
  quizAnswered.value = true;

  const currentQ = quizQuestions.value[currentQuizIndex.value];
  if (selectedOption === currentQ.correctAnswer) {
    quizScore.value++;
  }

  // Auto-avanzar después de 1.5s
  setTimeout(() => {
    nextQuizQuestion();
  }, 1500);
}

function nextQuizQuestion() {
  if (currentQuizIndex.value < quizQuestions.value.length - 1) {
    currentQuizIndex.value++;
    quizSelectedOption.value = null;
    quizAnswered.value = false;
  } else {
    quizFinished.value = true;
  }
}

function finishQuiz() {
  showVocabQuiz.value = false;
  quizFinished.value = false;
  // No necesitamos hacer nada más, el overlay de completado ya está visible
}

function skipQuiz() {
  showVocabQuiz.value = false;
  quizQuestions.value = [];
}

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
const errorMessage = ref("");

// Variables del Modo Ciego y de Ayuda Visual
const isBlindMode = ref(false);
const showBlindHint = ref(false);

// Variables de Estética y Sonidos (inicializados desde LocalStorage)
const selectedSoundTheme = ref(
  (typeof window !== "undefined" && localStorage.getItem("lbl_sound_theme")) ||
    "mechanical"
);
const lastActiveSoundTheme = ref(
  (typeof window !== "undefined" &&
    localStorage.getItem("lbl_sound_theme") !== "none" &&
    localStorage.getItem("lbl_sound_theme")) ||
    "mechanical"
);

function toggleSoundMute() {
  if (selectedSoundTheme.value === "none") {
    selectedSoundTheme.value = lastActiveSoundTheme.value;
  } else {
    lastActiveSoundTheme.value = selectedSoundTheme.value;
    selectedSoundTheme.value = "none";
  }
}

const selectedTheme = ref(
  (typeof window !== "undefined" && localStorage.getItem("lbl_visual_theme")) ||
    "default"
);
const selectedAiModel = ref(
  (typeof window !== "undefined" && localStorage.getItem("lbl_ai_model")) ||
    "gemini-2.5-flash"
);

// --- MEJORAS PREMIUM: CURSOR FLUIDO Y BARRA DE PROGRESO ---
const progressPercent = computed(() => {
  const total = activeLessonPhrases.value.length;
  if (total === 0) return 0;
  return (activePhraseIndex.value / total) * 100;
});

const caretStyle = ref({
  left: "0px",
  top: "0px",
  width: "0px",
  opacity: 0,
});

function updateCaret() {
  nextTick(() => {
    const lettersContainer = document.getElementById("target-phrase-letters");
    if (!lettersContainer) return;

    const activeChar = lettersContainer.querySelector(
      ".typing-caret"
    ) as HTMLElement;
    if (activeChar) {
      const containerRect = lettersContainer.getBoundingClientRect();
      const charRect = activeChar.getBoundingClientRect();

      caretStyle.value = {
        left: `${charRect.left - containerRect.left}px`,
        top: `${charRect.bottom - containerRect.top - 2}px`,
        width: `${charRect.width}px`,
        opacity: 1,
      };
    } else {
      caretStyle.value.opacity = 0;
    }
  });
}

// Sincronizadores para el cursor fluido
watch([typedText, activePhraseIndex, activeLessonId, selectedTheme], () => {
  updateCaret();
});

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
    audioCtx = new (
      window.AudioContext || (window as any).webkitAudioContext
    )();
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
      const notes = [523.25, 659.25, 783.99, 1046.5]; // C5, E5, G5, C6
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

function loadCompletedLessons() {
  const stored = localStorage.getItem("lbl_completed_lessons");
  if (stored) {
    try {
      completedLessons.value = JSON.parse(stored);
    } catch (e) {}
  }
}

function markLessonCompleted(id: string) {
  completedLessons.value[id] = true;
  localStorage.setItem(
    "lbl_completed_lessons",
    JSON.stringify(completedLessons.value)
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
      activeLessonId.value = "key_greetings";
      handleLessonChange();
    }
  }
}

// Options for dropdown
const lessonOptions = computed(() => {
  const lang = profile.value.language;
  const list = lessonsIndex.map((lesson) => ({
    value: lesson.id,
    label: lesson.title[lang] || lesson.title["es"],
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
  activeLessonId.value = "key_greetings";
  handleLessonChange();
}

function handleCustomLessonCreated({ lessonId, lesson }: { lessonId: string; lesson: Lesson }) {
  customLessons.value[lessonId] = lesson;
  saveCustomLessons();
  activeLessonId.value = lessonId;
  handleLessonChange();
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

  if (customLessons.value[id]) {
    phrases = [...customLessons.value[id].phrases];
  } else if (loadedLessonData.value) {
    phrases = [...loadedLessonData.value.phrases];
  } else if (id === "difficult_review") {
    // Cargar frases difíciles para repaso
    phrases = difficultPhrases.value.map((dp, idx) => ({
      ...dp.phrase,
      id: `diff-${idx}`,
    }));
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
    let syllables: string[] = [];

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
  "partOfSpeech": "noun, verb, adjective, adverb",
  "syllables": ["sí", "la", "bas"]
}
El campo "syllables" debe contener la palabra separada en sus sílabas fonéticas en inglés (cada sílaba como un string separado). Si la palabra tiene una sola sílaba, retorna un array con un solo elemento.
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
          if (parsed.syllables && Array.isArray(parsed.syllables)) {
            syllables = parsed.syllables;
          }
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
      syllables: syllables.length > 0 ? syllables : undefined,
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
  return (
    phrases.length > 0 &&
    activePhraseIndex.value >= phrases.length &&
    !showVocabQuiz.value
  );
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

// Scroll wrapper to center target typing box vertically in current viewport
function scrollTypingBoxIntoView() {
  nextTick(() => {
    const container = document.getElementById("typing-box-container");
    if (container) {
      container.scrollIntoView({
        behavior: "smooth",
        block: "center",
      });
    }
  });
}

function handleVisualViewportResize() {
  if (typeof window !== "undefined" && window.visualViewport) {
    const isKeyboardOpen =
      window.visualViewport.height < window.innerHeight * 0.85;
    if (isKeyboardOpen && document.activeElement === inputRef.value) {
      scrollTypingBoxIntoView();
    }
  }
}

// Input control
function focusInput() {
  if (inputRef.value) {
    inputRef.value.focus();
  }
  updateCaret();

  if (typeof window !== "undefined" && window.innerWidth < 768) {
    setTimeout(scrollTypingBoxIntoView, 150);
  }
}

function handleInput(e: Event) {
  const target = e.target as HTMLInputElement;
  typedText.value = target.value;
  processInput();
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
  const currentCapped =
    current.length > target.length ? current.slice(0, target.length) : current;

  // Disparar sonido de tecleo al añadir un nuevo carácter en base al texto limitado
  if (currentCapped.length > lastTypedLength) {
    const lastCharIndex = currentCapped.length - 1;
    const targetChar = target[lastCharIndex];
    if (
      targetChar &&
      currentCapped[lastCharIndex].toLowerCase() === targetChar.toLowerCase()
    ) {
      playKeyPressSound("correct");
    } else {
      playKeyPressSound("incorrect");
      currentPhraseErrors.value++;
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

    // --- FEATURE 1: Detectar frases difíciles ---
    // Calcular accuracy real del tipeo de esta frase en base a los errores cometidos en el camino
    const phraseLength = target.length;
    const correctKeys = Math.max(0, phraseLength - currentPhraseErrors.value);
    const phraseAccuracy =
      phraseLength > 0 ? (correctKeys / phraseLength) * 100 : 100;
    if (phraseAccuracy < 85 && activeLessonId.value !== "difficult_review") {
      addDifficultPhrase(
        phrases[activePhraseIndex.value],
        activeLessonId.value,
        phraseAccuracy
      );
    }
    // Si la frase estaba en repaso y la accuracy fue buena, removerla
    if (phraseAccuracy >= 85 && activeLessonId.value === "difficult_review") {
      const dpIdx = difficultPhrases.value.findIndex(
        (dp) => dp.phrase.text.toLowerCase() === target.toLowerCase()
      );
      if (dpIdx !== -1) {
        removeDifficultPhrase(dpIdx);
      }
    }
    currentPhraseErrors.value = 0; // Reiniciar para la siguiente frase

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

      if (activeLessonId.value !== "difficult_review" && activeLessonId.value !== "custom_practice") {
        markLessonCompleted(activeLessonId.value);
      }

      // --- FEATURE 3: Lanzar Quiz de Vocabulario ---
      const hasQuiz = generateQuizQuestions();
      if (hasQuiz) {
        showVocabQuiz.value = true;
      }
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

      if (activeLessonId.value !== "difficult_review" && activeLessonId.value !== "custom_practice") {
        markLessonCompleted(activeLessonId.value);
      }
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
  newDifficultCount.value = 0;
  currentPhraseErrors.value = 0;
  showVocabQuiz.value = false;
  quizQuestions.value = [];
  quizFinished.value = false;
  if (inputRef.value) {
    inputRef.value.value = "";
  }
}

async function loadLessonData(lessonId: string) {
  activeLessonId.value = lessonId;
  activePhraseIndex.value = 0;
  completedPhrases.value = [];
  resetSession();

  if (lessonId === "create_new_lesson") return;

  // Carga asíncrona si es una lección estándar del índice
  const isStandard = lessonsIndex.some((l) => l.id === lessonId);
  if (isStandard) {
    const path = `../data/lessons/${lessonId}.json`;
    const loadFn = lessonsModules[path];
    if (loadFn) {
      try {
        const module = (await loadFn()) as any;
        loadedLessonData.value = module.default;
      } catch (err) {
        console.error(`Error loading lesson ${lessonId}:`, err);
        errorMessage.value = `Error al cargar la lección: ${err}`;
      }
    }
  } else {
    // Si no es estándar, vaciar la lección estática cargada
    loadedLessonData.value = null;
  }

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
  let ids = lessonsIndex.map((l) => l.id);
  if (customLessons.value[activeLessonId.value]) {
    ids = Object.keys(customLessons.value);
  }

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
  loadLessonData("key_greetings");
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
  loadCompletedLessons();
  loadDifficultPhrases();
  loadLessonData("key_greetings");
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
  window.addEventListener("resize", updateCaret);
  if (typeof window !== "undefined" && window.visualViewport) {
    window.visualViewport.addEventListener(
      "resize",
      handleVisualViewportResize
    );
  }
  setTimeout(updateCaret, 250);
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
  window.removeEventListener("resize", updateCaret);
  if (typeof window !== "undefined" && window.visualViewport) {
    window.visualViewport.removeEventListener(
      "resize",
      handleVisualViewportResize
    );
  }
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
  aside {
    position: fixed !important;
    left: 0;
    top: 0;
    bottom: 0;
    z-index: 50;
    height: 100vh;
    max-height: 100vh !important;
    width: 80vw !important;
    max-width: 320px !important;
    box-shadow: 10px 0 30px rgba(0, 0, 0, 0.15);
    background-color: rgb(255, 255, 255) !important;
  }

  .dark aside {
    background-color: rgb(15, 23, 42) !important;
  }

  .sidebar-enter-active,
  .sidebar-leave-active {
    transition:
      transform 0.35s cubic-bezier(0.16, 1, 0.3, 1),
      opacity 0.35s cubic-bezier(0.16, 1, 0.3, 1) !important;
    max-height: 100vh !important;
  }

  .sidebar-enter-from,
  .sidebar-leave-to {
    opacity: 0;
    transform: translateX(-100%) !important;
    max-height: 100vh !important;
    padding-left: 0 !important;
    padding-right: 0 !important;
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
  height: 1.1em;
  line-height: 1.1em;
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
  box-shadow:
    0 0 10px #ff007f,
    0 0 20px #ff007f !important;
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
