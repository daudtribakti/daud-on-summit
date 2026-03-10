<script setup lang="ts">
import gsap from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'

gsap.registerPlugin(ScrollTrigger)

const showSplash = ref(true)
const scrollY = ref(0)
const loadingPercent = ref(0)

let splashTimeout: ReturnType<typeof setTimeout> | undefined
let percentInterval: ReturnType<typeof setInterval> | undefined

const onScroll = () => {
  scrollY.value = window.scrollY || 0
}

const scrollProgress = computed(() => {
  if (typeof document === 'undefined') return 0
  const doc = document.documentElement
  const total = doc.scrollHeight - doc.clientHeight
  if (total <= 0) return 0
  return Math.min(1, (scrollY.value / total) * 1.2)
})

const SPLASH_DURATION_MS = 5500

onMounted(() => {
  const start = Date.now()
  percentInterval = setInterval(() => {
    const elapsed = Date.now() - start
    const p = Math.min(100, Math.floor((elapsed / SPLASH_DURATION_MS) * 100))
    loadingPercent.value = p
  }, 50)

  splashTimeout = setTimeout(() => {
    loadingPercent.value = 100
    if (percentInterval) {
      clearInterval(percentInterval)
      percentInterval = undefined
    }
    showSplash.value = false
  }, SPLASH_DURATION_MS)

  window.addEventListener('scroll', onScroll, { passive: true })
  nextTick(() => runAboutReveal())
})

onBeforeUnmount(() => {
  if (splashTimeout) clearTimeout(splashTimeout)
  if (percentInterval) clearInterval(percentInterval)
  window.removeEventListener('scroll', onScroll)
})

// Refs for hero entrance animation
const heroBadge = ref<HTMLElement | null>(null)
const heroLabel = ref<HTMLElement | null>(null)
const heroTitle = ref<HTMLElement | null>(null)
const heroDesc = ref<HTMLElement | null>(null)
const heroActions = ref<HTMLElement | null>(null)

const runHeroIntro = () => {
  nextTick(() => {
    const els = [
      heroBadge.value,
      heroLabel.value,
      heroTitle.value,
      heroDesc.value,
      heroActions.value
    ].filter(Boolean) as HTMLElement[]

    if (!els.length) {
      return
    }

    gsap.fromTo(
      els,
      { opacity: 0, y: 32 },
      {
        opacity: 1,
        y: 0,
        duration: 0.8,
        stagger: 0.12,
        ease: 'power3.out'
      }
    )
  })
}

// Refs untuk animasi section tentang
const aboutSection = ref<HTMLElement | null>(null)

const runAboutReveal = () => {
  nextTick(() => {
    if (!aboutSection.value) return
    const heading = aboutSection.value.querySelector('h2')
    const card = aboutSection.value.querySelector('[data-about-card]')

    gsap.fromTo(
      heading,
      { opacity: 0, y: 28 },
      {
        opacity: 1,
        y: 0,
        duration: 0.7,
        ease: 'power3.out',
        scrollTrigger: {
          trigger: aboutSection.value,
          start: 'top 82%',
          toggleActions: 'play none none none'
        }
      }
    )
    gsap.fromTo(
      card,
      { opacity: 0, y: 24 },
      {
        opacity: 1,
        y: 0,
        duration: 0.7,
        delay: 0.15,
        ease: 'power3.out',
        scrollTrigger: {
          trigger: aboutSection.value,
          start: 'top 82%',
          toggleActions: 'play none none none'
        }
      }
    )
  })
}

const backgroundOffset = computed(
  () => `translate3d(0, ${scrollY.value * 0.25}px, 0) scale(1.1)`
)
const mountainsOffset = computed(
  () => `translate3d(0, ${scrollY.value * 0.18}px, 0) scale(1.05)`
)
const mistOffset = computed(
  () => `translate3d(0, ${scrollY.value * 0.1}px, 0)`
)

const scrollToAbout = () => {
  const el = document.getElementById('tentang-daud')
  if (el) {
    el.scrollIntoView({ behavior: 'smooth', block: 'start' })
  }
}
</script>

<template>
  <div class="relative overflow-hidden bg-background min-h-screen">
    <!-- Scroll progress bar -->
    <div
      class="fixed top-0 left-0 right-0 z-50 h-0.5 bg-primary/90 transition-transform duration-150 ease-out origin-left"
      :style="{ transform: `scaleX(${scrollProgress})` }"
    />

    <div
      class="fixed inset-0 pointer-events-none z-[1] opacity-[0.035] mix-blend-overlay grain-texture"
      aria-hidden="true"
    />

    <transition
      name="splash-fade"
      @after-leave="runHeroIntro"
    >
      <div
        v-if="showSplash"
        class="fixed inset-0 z-40 flex flex-col items-center justify-center bg-gradient-to-b from-slate-900 via-slate-950 to-slate-900 text-white splash-fade-in px-4 safe-area-padding"
      >
        <div class="relative flex flex-col items-center gap-4 sm:gap-6 w-full max-w-md">
          <div class="w-24 h-24 sm:w-32 sm:h-32 rounded-full bg-white/5 border border-white/10 flex items-center justify-center shadow-2xl shadow-black/40 shrink-0">
            <UIcon
              name="i-lucide-mountain"
              class="w-12 h-12 sm:w-16 sm:h-16 text-primary animate-mountain-pulse"
            />
          </div>

          <div class="text-center space-y-2">
            <p class="text-[10px] sm:text-xs uppercase tracking-[0.2em] sm:tracking-[0.25em] text-white/60">
              Menyapa dari Kaki Gunung
            </p>
            <h1 class="text-xl sm:text-3xl md:text-4xl font-semibold tracking-tight leading-tight">
              Daud Tri Bakti Purba
            </h1>
            <p class="max-w-md text-xs sm:text-base text-white/70 mx-auto px-1">
              Mimpi: mendaki semua gunung di Indonesia, satu puncak demi satu puncak.
            </p>
          </div>

          <div class="mt-2 sm:mt-3 flex flex-col items-center gap-2 sm:gap-3 text-sm text-white/70 w-full">
            <div class="splash-loading-track h-2 w-40 sm:w-48 rounded-full bg-white/10 overflow-hidden max-w-full">
              <div
                class="splash-loading-bar h-full rounded-full transition-[width] duration-75 ease-out"
                :style="{ width: `${loadingPercent}%` }"
              />
            </div>
            <p class="text-sm tabular-nums font-medium text-white/90">
              {{ loadingPercent }}%
            </p>
            <p class="text-[11px] uppercase tracking-[0.22em] text-white/60">
              Menyiapkan basecamp digital kamu...
            </p>
          </div>
        </div>
      </div>
    </transition>

    <section class="relative min-h-[100svh] h-[100svh] md:min-h-screen md:h-auto">
      <div
        class="pointer-events-none absolute inset-x-0 top-[-10vh] h-[120vh] md:bg-fixed bg-cover opacity-95 will-change-transform"
        :style="{
          transform: backgroundOffset,
          backgroundImage: 'url(/images/pegunungan-hero.jpg)',
          backgroundPosition: 'center 60%'
        }"
      />

      <div
        class="pointer-events-none absolute inset-x-0 bottom-0 h-[40vh] will-change-transform"
        :style="{ transform: mountainsOffset }"
      />

      <div
        class="pointer-events-none absolute inset-x-0 top-[10vh] h-[40vh] will-change-transform"
        :style="{ transform: mistOffset }"
      >
        <div class="h-full bg-gradient-to-b from-slate-200/35 via-slate-200/10 to-transparent dark:from-slate-200/15 dark:via-slate-900/10 blur-2xl" />
      </div>

      <div class="pointer-events-none absolute inset-x-0 bottom-0 h-[45vh] bg-gradient-to-b from-transparent via-slate-950/55 to-slate-950/85" />

      <div class="pointer-events-none absolute inset-0 z-[2] bg-gradient-to-r from-slate-950/70 via-slate-950/30 to-transparent" />
      <div class="pointer-events-none absolute inset-0 z-[3] hero-vignette" />

      <div class="relative z-10">
        <UContainer class="relative pt-24 pb-16 px-4 sm:px-6 sm:pt-28 sm:pb-20 md:pt-36 md:pb-28">
          <div class="hero-content-block relative flex flex-col gap-6 sm:gap-8 md:gap-10 max-w-2xl">
            <div
              ref="heroBadge"
              class="hero-anim-item hero-badge hero-badge-float inline-flex items-center gap-2.5 sm:gap-3 rounded-full bg-slate-950/50 border border-white/20 px-3 py-2 sm:px-4 sm:py-2.5 backdrop-blur-xl w-fit max-w-full shadow-[0_0_0_1px_rgba(255,255,255,0.06),0_8px_32px_rgba(0,0,0,0.4)]"
            >
              <div class="flex items-center justify-center w-8 h-8 sm:w-9 sm:h-9 rounded-full bg-primary/30 text-primary-50 shadow-[0_0_20px_rgba(0,220,130,0.25)] shrink-0">
                <UIcon
                  name="i-lucide-mountain"
                  class="w-3.5 h-3.5 sm:w-4 sm:h-4"
                />
              </div>
              <div class="leading-tight min-w-0">
                <p class="text-[10px] sm:text-[11px] uppercase tracking-[0.2em] sm:tracking-[0.24em] text-white/80 truncate">
                  Daud Tri Bakti Purba
                </p>
                <p class="text-[11px] sm:text-xs font-medium text-white/95 line-clamp-2 sm:line-clamp-none">
                  Misi Mendaki Gunung Indonesia
                </p>
              </div>
            </div>

            <div class="space-y-4 sm:space-y-6">
              <p
                ref="heroLabel"
                class="hero-anim-item text-[10px] sm:text-[11px] uppercase tracking-[0.24em] sm:tracking-[0.32em] text-emerald-400/90 font-medium"
              >
                Jejak Puncak Indonesia
              </p>

              <h1
                ref="heroTitle"
                class="hero-anim-item hero-title text-2xl sm:text-4xl md:text-5xl lg:text-[2.75rem] font-semibold leading-[1.2] sm:leading-[1.15] tracking-tight hero-title-gradient"
              >
                Misi Mendaki Semua Gunung di Indonesia
              </h1>

              <p
                ref="heroDesc"
                class="hero-anim-item text-sm sm:text-lg text-slate-200/95 leading-relaxed max-w-xl"
              >
                Ini adalah rumah digital perjalanan saya, Daud Tri Bakti Purba, dalam mengejar mimpi menapaki puncak-puncak Nusantara — dari Sabang sampai Merauke, dari gunung yang populer hingga yang sepi dikunjungi.
              </p>

              <div
                ref="heroActions"
                class="hero-anim-item flex flex-col sm:flex-row flex-wrap items-stretch sm:items-center gap-3 sm:gap-4 pt-2"
              >
                <UButton
                  color="primary"
                  size="lg"
                  class="hero-cta-primary rounded-full shadow-lg shadow-emerald-500/35 px-6 sm:px-8 py-3 min-h-[44px] transition-all duration-300 hover:shadow-xl hover:shadow-emerald-500/50 hover:scale-[1.02] hover:shadow-[0_0_28px_rgba(0,220,130,0.4)] w-full sm:w-auto justify-center"
                  @click="scrollToAbout"
                >
                  Lihat mimpi saya
                </UButton>

                <button
                  type="button"
                  class="hero-cta-secondary group inline-flex items-center justify-center gap-2 text-sm text-slate-200/90 hover:text-white transition-all duration-300 min-h-[44px] py-2"
                  @click="scrollToAbout"
                >
                  <span class="border-b border-slate-400/50 group-hover:border-emerald-400/80 transition-colors">
                    Scroll ke cerita
                  </span>
                  <UIcon
                    name="i-lucide-arrow-down-right"
                    class="w-4 h-4 transition-transform duration-300 group-hover:translate-x-0.5 group-hover:-translate-y-0.5 shrink-0"
                  />
                </button>
              </div>
            </div>
          </div>

          <div
            class="hero-scroll-hint absolute bottom-4 sm:bottom-6 left-1/2 -translate-x-1/2 flex flex-col items-center gap-1.5 text-white/50 transition-opacity duration-500"
            :class="scrollY > 60 ? 'opacity-0 pointer-events-none' : 'opacity-100'"
          >
            <span class="text-[10px] uppercase tracking-[0.2em]">Gulir</span>
            <UIcon
              name="i-lucide-chevron-down"
              class="w-4 h-4 animate-bounce"
            />
          </div>
        </UContainer>
      </div>
    </section>

    <section
      ref="aboutSection"
      id="tentang-daud"
      class="relative bg-gradient-to-b from-slate-950/70 via-slate-950 to-slate-950 pt-12 sm:pt-16 md:pt-20"
    >
      <UContainer class="relative z-10 pb-12 sm:pb-16 md:pb-20 px-4 sm:px-6">
        <div class="grid gap-8 sm:gap-10 md:grid-cols-[minmax(0,1.4fr)_minmax(0,1fr)] items-start">
          <div class="space-y-3 sm:space-y-4">
            <h2 class="text-xl sm:text-2xl md:text-3xl font-semibold tracking-tight text-foreground">
              Tentang Saya & Mimpi Ini
            </h2>
            <p class="text-xs sm:text-sm text-muted uppercase tracking-[0.18em] sm:tracking-[0.2em]">
              Daud Tri Bakti Purba · Pendaki Indonesia
            </p>
            <p class="text-sm sm:text-base text-foreground/90 leading-relaxed">
              Saya Daud, seorang pendaki yang jatuh cinta pada sunyinya jalur setapak, udara dingin pagi hari di camp, dan hangatnya matahari di puncak. Website ini adalah tempat saya menyimpan mimpi:
              mendaki semua gunung yang ada di Indonesia dan membawanya pulang dalam bentuk cerita.
            </p>
            <p class="text-sm sm:text-base text-foreground/80 leading-relaxed">
              Bukan hanya soal menaklukkan ketinggian, tapi juga menghargai setiap langkah kecil, teman seperjalanan, dan keindahan alam Indonesia yang begitu kaya. Setiap puncak adalah pengingat bahwa mimpi besar selalu dimulai dari satu langkah berani.
            </p>
          </div>

          <div
            data-about-card
            class="about-card-hover space-y-3 sm:space-y-4 rounded-xl sm:rounded-2xl border border-slate-100/20 bg-slate-900/40 backdrop-blur-lg p-4 sm:p-5 md:p-6 shadow-[0_18px_45px_rgba(0,0,0,0.65)] transition-all duration-300"
          >
            <h3 class="text-sm font-semibold text-foreground/90 uppercase tracking-[0.16em]">
              Sekilas Perjalanan
            </h3>
            <div class="space-y-3 text-sm text-muted">
              <p>
                • Target jangka panjang: semua gunung di Indonesia<br>
                • Fokus awal: Jawa, Sumatra, dan Nusa Tenggara<br>
                • Nilai utama: keselamatan, kebersamaan, dan menjaga alam
              </p>
              <p>
                Ke depan, halaman ini akan berkembang menjadi catatan pendakian, daftar gunung, dan progres perjalanan. Untuk sekarang, anggap ini sebagai basecamp digital sebelum menuju puncak-puncak berikutnya.
              </p>
            </div>
          </div>
        </div>
      </UContainer>
    </section>
  </div>
</template>

<style scoped>
.hero-vignette {
  background: radial-gradient(
    ellipse 80% 60% at 20% 50%,
    transparent 0%,
    transparent 50%,
    rgba(2, 6, 23, 0.15) 100%
  );
}

.hero-content-block {
  text-shadow: 0 2px 20px rgba(0, 0, 0, 0.35), 0 1px 2px rgba(0, 0, 0, 0.2);
}

.hero-title {
  text-shadow:
    0 4px 28px rgba(0, 0, 0, 0.55),
    0 0 1px rgba(0, 0, 0, 0.35);
}

.hero-title-gradient {
  color: #fdfdfd;
}

.hero-badge-float {
  animation: hero-badge-float 6s ease-in-out infinite;
}

@keyframes hero-badge-float {
  0%,
  100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-4px);
  }
}

.hero-badge:hover {
  border-color: rgba(255, 255, 255, 0.25);
  box-shadow: 0 0 0 1px rgba(255, 255, 255, 0.08), 0 12px 40px rgba(0, 0, 0, 0.45);
}

.splash-loading-track {
  position: relative;
}

.splash-loading-bar {
  background: linear-gradient(
    90deg,
    rgba(52, 211, 153, 0.6) 0%,
    rgba(56, 189, 248, 0.9) 50%,
    rgba(129, 140, 248, 0.8) 100%
  );
  min-width: 4px;
  box-shadow: 0 0 12px rgba(52, 211, 153, 0.4);
}

.about-card-hover:hover {
  transform: translateY(-4px);
  border-color: rgba(52, 211, 153, 0.25);
  box-shadow:
    0 24px 50px rgba(0, 0, 0, 0.6),
    0 0 0 1px rgba(52, 211, 153, 0.1),
    0 0 40px rgba(52, 211, 153, 0.08);
}
</style>
