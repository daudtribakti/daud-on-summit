<script setup lang="ts">
import gsap from 'gsap'

const showSplash = ref(true)
const scrollY = ref(0)
const loadingPercent = ref(0)

let splashTimeout: ReturnType<typeof setTimeout> | undefined
let percentInterval: ReturnType<typeof setInterval> | undefined

const onScroll = () => {
  scrollY.value = window.scrollY || 0
}

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

const backgroundOffset = computed(
  () => `translate3d(0, ${scrollY.value * 0.25}px, 0) scale(1.1)`
)
const mountainsOffset = computed(
  () => `translate3d(0, ${scrollY.value * 0.18}px, 0) scale(1.05)`
)
const mistOffset = computed(
  () => `translate3d(0, ${scrollY.value * 0.1}px, 0)`
)
const aboutOffset = computed(
  () => `translate3d(0, ${scrollY.value * -0.03}px, 0)`
)

const scrollToAbout = () => {
  const el = document.getElementById('tentang-daud')
  if (el) {
    el.scrollIntoView({ behavior: 'smooth', block: 'start' })
  }
}
</script>

<template>
  <div class="relative overflow-hidden bg-background">
    <transition
      name="splash-fade"
      @after-leave="runHeroIntro"
    >
      <div
        v-if="showSplash"
        class="fixed inset-0 z-40 flex flex-col items-center justify-center bg-gradient-to-b from-slate-900 via-slate-950 to-slate-900 text-white splash-fade-in"
      >
        <div class="relative flex flex-col items-center gap-6">
          <div class="w-32 h-32 rounded-full bg-white/5 border border-white/10 flex items-center justify-center shadow-2xl shadow-black/40">
            <UIcon
              name="i-lucide-mountain"
              class="w-16 h-16 text-primary animate-mountain-pulse"
            />
          </div>

          <div class="text-center space-y-2">
            <p class="text-xs uppercase tracking-[0.25em] text-white/60">
              Menyapa dari Kaki Gunung
            </p>
            <h1 class="text-2xl sm:text-3xl md:text-4xl font-semibold tracking-tight">
              Daud Tri Bakti Purba
            </h1>
            <p class="max-w-md text-sm sm:text-base text-white/70 mx-auto">
              Mimpi: mendaki semua gunung di Indonesia, satu puncak demi satu puncak.
            </p>
          </div>

          <div class="mt-3 flex flex-col items-center gap-3 text-sm text-white/70">
            <div class="splash-loading-track h-2 w-48 rounded-full bg-white/10 overflow-hidden">
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

    <section class="relative min-h-screen">
      <div
        class="pointer-events-none absolute inset-x-0 top-[-10vh] h-[120vh] bg-fixed bg-cover opacity-95 will-change-transform"
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

      <div class="relative z-10">
        <div class="pointer-events-none absolute inset-0 bg-gradient-to-r from-slate-950/70 via-slate-950/30 to-transparent" />
        <div class="pointer-events-none absolute inset-0 hero-vignette" />

        <UContainer class="relative pt-28 pb-20 md:pt-36 md:pb-28">
          <div class="hero-content-block relative flex flex-col gap-10 max-w-2xl">
            <div
              ref="heroBadge"
              class="hero-anim-item hero-badge inline-flex items-center gap-3 rounded-full bg-slate-950/50 border border-white/20 px-4 py-2.5 backdrop-blur-xl w-fit shadow-[0_0_0_1px_rgba(255,255,255,0.06),0_8px_32px_rgba(0,0,0,0.4)]"
            >
              <div class="flex items-center justify-center w-9 h-9 rounded-full bg-primary/30 text-primary-50 shadow-[0_0_20px_rgba(0,220,130,0.25)]">
                <UIcon
                  name="i-lucide-mountain"
                  class="w-4 h-4"
                />
              </div>
              <div class="leading-tight">
                <p class="text-[11px] uppercase tracking-[0.24em] text-white/80">
                  Daud Tri Bakti Purba
                </p>
                <p class="text-xs font-medium text-white/95">
                  Misi Mendaki Gunung Indonesia
                </p>
              </div>
            </div>

            <div class="space-y-6">
              <p
                ref="heroLabel"
                class="hero-anim-item text-[11px] uppercase tracking-[0.32em] text-emerald-400/90 font-medium"
              >
                Jejak Puncak Indonesia
              </p>

              <h1
                ref="heroTitle"
                class="hero-anim-item hero-title text-3xl sm:text-4xl md:text-5xl lg:text-[2.75rem] font-semibold leading-[1.15] tracking-tight text-white"
              >
                Misi Mendaki Semua Gunung di Indonesia
              </h1>

              <p
                ref="heroDesc"
                class="hero-anim-item text-base sm:text-lg text-slate-200/95 leading-relaxed max-w-xl"
              >
                Ini adalah rumah digital perjalanan saya, Daud Tri Bakti Purba, dalam mengejar mimpi menapaki puncak-puncak Nusantara — dari Sabang sampai Merauke, dari gunung yang populer hingga yang sepi dikunjungi.
              </p>

              <div
                ref="heroActions"
                class="hero-anim-item flex flex-wrap items-center gap-4 pt-2"
              >
                <UButton
                  color="primary"
                  size="lg"
                  class="hero-cta-primary rounded-full shadow-lg shadow-emerald-500/35 px-8 transition-all duration-300 hover:shadow-xl hover:shadow-emerald-500/45 hover:scale-[1.02]"
                  @click="scrollToAbout"
                >
                  Lihat mimpi saya
                </UButton>

                <button
                  type="button"
                  class="hero-cta-secondary group inline-flex items-center gap-2 text-sm text-slate-200/90 hover:text-white transition-all duration-300"
                  @click="scrollToAbout"
                >
                  <span class="border-b border-slate-400/50 group-hover:border-emerald-400/80 transition-colors">
                    Scroll ke cerita
                  </span>
                  <UIcon
                    name="i-lucide-arrow-down-right"
                    class="w-4 h-4 transition-transform duration-300 group-hover:translate-x-0.5 group-hover:-translate-y-0.5"
                  />
                </button>
              </div>
            </div>
          </div>

          <div
            class="hero-scroll-hint absolute bottom-6 left-1/2 -translate-x-1/2 flex flex-col items-center gap-1.5 text-white/50 transition-opacity duration-500"
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
      id="tentang-daud"
      class="relative bg-gradient-to-b from-slate-950/70 via-slate-950 to-slate-950 pt-16 md:pt-20"
    >
      <UContainer class="relative z-10 pb-16 md:pb-20">
        <div class="grid gap-10 md:grid-cols-[minmax(0,1.4fr)_minmax(0,1fr)] items-start">
          <div class="space-y-4">
            <h2 class="text-2xl md:text-3xl font-semibold tracking-tight text-foreground">
              Tentang Saya & Mimpi Ini
            </h2>
            <p class="text-sm text-muted uppercase tracking-[0.2em]">
              Daud Tri Bakti Purba · Pendaki Indonesia
            </p>
            <p class="text-base text-foreground/90 leading-relaxed">
              Saya Daud, seorang pendaki yang jatuh cinta pada sunyinya jalur setapak, udara dingin pagi hari di camp, dan hangatnya matahari di puncak. Website ini adalah tempat saya menyimpan mimpi:
              mendaki semua gunung yang ada di Indonesia dan membawanya pulang dalam bentuk cerita.
            </p>
            <p class="text-base text-foreground/80 leading-relaxed">
              Bukan hanya soal menaklukkan ketinggian, tapi juga menghargai setiap langkah kecil, teman seperjalanan, dan keindahan alam Indonesia yang begitu kaya. Setiap puncak adalah pengingat bahwa mimpi besar selalu dimulai dari satu langkah berani.
            </p>
          </div>

          <div
            class="space-y-4 rounded-2xl border border-slate-100/20 bg-slate-900/40 backdrop-blur-lg p-5 md:p-6 shadow-[0_18px_45px_rgba(0,0,0,0.65)]"
            :style="{ transform: aboutOffset }"
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
  text-shadow: 0 4px 24px rgba(0, 0, 0, 0.4), 0 0 1px rgba(0, 0, 0, 0.3);
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
</style>
