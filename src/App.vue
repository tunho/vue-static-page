<template>
  <main class="hangawi" role="main" aria-label="한가위 테마 랜딩">
    <header class="topbar">
      <div class="brand">
        <span class="seal" aria-hidden="true">秋夕</span>
        <h1>풍성한 한가위</h1>
      </div>

      <div class="controls">
        <label class="ctrl">
          <span>보름달 크기</span>
          <input
            type="range"
            min="0.8"
            max="1.4"
            step="0.01"
            v-model.number="moonScale"
            aria-label="보름달 크기 조절"
          />
        </label>

        <label class="ctrl toggle">
          <input type="checkbox" v-model="showLanterns" />
          <span>등불</span>
        </label>

        <button class="share" @click="share">
          공유
        </button>
      </div>
    </header>

    <section class="hero" @click="spark()">
      <!-- 하늘 배경 결 -->
      <div class="hanji" aria-hidden="true"></div>

      <!-- 달 -->
      <svg
        class="moon"
        :style="{ transform: `translate(-50%, -50%) scale(${moonScale})` }"
        viewBox="0 0 200 200"
        aria-label="추석 보름달"
      >
        <defs>
          <radialGradient id="g" cx="50%" cy="45%" r="60%">
            <stop offset="0%" stop-color="#fff9d6" />
            <stop offset="55%" stop-color="#ffe48a" />
            <stop offset="100%" stop-color="#f6c85c" />
          </radialGradient>
        </defs>
        <circle cx="100" cy="100" r="90" fill="url(#g)" />
        <circle cx="70" cy="85" r="10" fill="#e7b957" opacity="0.35" />
        <circle cx="125" cy="120" r="14" fill="#e7b957" opacity="0.28" />
        <circle cx="105" cy="70" r="6" fill="#e7b957" opacity="0.3" />
      </svg>

      <!-- 학 (장식) -->
      <div class="crane c1" aria-hidden="true">
        <svg viewBox="0 0 120 60">
          <path d="M5,35 C35,10 85,10 115,35" fill="none" stroke="white" stroke-width="2" />
          <circle cx="60" cy="28" r="2" fill="white" />
        </svg>
      </div>
      <div class="crane c2" aria-hidden="true">
        <svg viewBox="0 0 120 60">
          <path d="M5,35 C35,10 85,10 115,35" fill="none" stroke="white" stroke-width="2" />
          <circle cx="60" cy="28" r="2" fill="white" />
        </svg>
      </div>

      <!-- 등불 -->
      <div v-if="showLanterns" class="lantern left" aria-label="추석 등불">
        <div class="rope"></div>
        <div class="body">
          <div class="rib"></div><div class="rib"></div><div class="rib"></div>
        </div>
        <div class="tassel"></div>
      </div>
      <div v-if="showLanterns" class="lantern right" aria-label="추석 등불">
        <div class="rope"></div>
        <div class="body">
          <div class="rib"></div><div class="rib"></div><div class="rib"></div>
        </div>
        <div class="tassel"></div>
      </div>

      <!-- 소원 문구 -->
      <div class="wish">
        <p class="wish-text">{{ currentWish }}</p>
        <div class="wish-actions">
          <button class="btn" @click.stop="shuffleWish">소원 바꾸기</button>
        </div>
      </div>

      <!-- 반짝 불빛 -->
      <div
        v-for="s in sparks"
        :key="s.id"
        class="spark"
        :style="{ left: s.x + 'vw', top: s.y + 'vh' }"
        aria-hidden="true"
      />
    </section>

    <!-- 카드 섹션 -->
    <section class="cards">
      <article class="card">
        <h3>송편 한 알</h3>
        <p>달을 보고 마음을 모아, 소망 하나 정성껏 채워요.</p>
      </article>
      <article class="card">
        <h3>가족 안부</h3>
        <p>멀리 있어도 따뜻한 말 한마디면 마음의 거리는 0.</p>
      </article>
      <article class="card">
        <h3>감사 기록</h3>
        <p>오늘 고마웠던 일 3가지, 메모해두면 행복이 커져요.</p>
      </article>
    </section>

    <footer class="footer">
      <small>© 2025 Hangawi · 클릭으로 반짝이를 만들 수 있어요 ✨</small>
    </footer>
  </main>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'

const wishes = [
  '보름달처럼 환하고 둥근 한 해가 되길 ✨',
  '가족 모두 건강하고 평온하길 🌕',
  '시작한 일들이 알차게 맺어지길 🍂',
  '행운이 가득하고 근심은 가볍길 🌾',
  '마음의 평화와 쉼이 함께하길 🕊️',
  '도전에는 용기를, 노력에는 보람을 💪',
  '사랑이 깃들고 웃음이 넘치길 😊',
]

const currentWish = ref<string>(wishes[0] ?? "")
const showLanterns = ref(true)
const moonScale = ref<number>(1.0)
const calm = ref(false)

type Spark = { id: number; x: number; y: number }
const sparks = ref<Spark[]>([])
let sid = 0

function shuffleWish() {
  const pick = Math.floor(Math.random() * wishes.length)
  currentWish.value = wishes[pick] ?? ""
}

function spark() {
  if (calm.value) return
  const s: Spark = {
    id: ++sid,
    x: 10 + Math.random() * 80,
    y: 10 + Math.random() * 60,
  }
  sparks.value.push(s)
  // 자동 소멸
  setTimeout(() => {
    sparks.value = sparks.value.filter((v) => v.id !== s.id)
  }, 2000)
}


async function share() {
  const text = `풍성한 한가위 🍂\n${currentWish.value}`
  const shareData = {
    title: '풍성한 한가위',
    text,
    url: location.href,
  }
  try {
    if (navigator.share) {
      await navigator.share(shareData)
    } else {
      await navigator.clipboard.writeText(`${text}\n${location.href}`)
      alert('클립보드에 복사했어요!')
    }
  } catch {
    // 취소 또는 미지원
  }
}

onMounted(() => {
  // 초기 반짝이 몇 개
  for (let i = 0; i < 6; i++) setTimeout(spark, 200 + i * 120)
})
</script>

<style scoped>
/* 색상 변수 */
:root {
  --bg: #0b1220;
  --bg2: #10182b;
  --gold: #f6c85c;
  --gold-dim: #e7b957;
  --ink: #d8dee9;
  --muted: #9aa5b1;
  --card: #141d33;
  --accent: #c9f0ff;
}

* { box-sizing: border-box; }
html, body, .hangawi { height: 100%; }
body { margin: 0; background: var(--bg); color: var(--ink); font-family: system-ui, -apple-system, "Segoe UI", Roboto, Apple SD Gothic Neo, "Noto Sans KR", "Malgun Gothic", sans-serif; }

.hangawi {
  display: grid;
  grid-template-rows: auto 1fr auto auto;
  min-height: 100vh;
  background:
    radial-gradient(80% 120% at 50% 0%, #0f1a33 0%, var(--bg) 60%) no-repeat;
}

/* 상단 */
.topbar {
  display: flex; align-items: center; justify-content: space-between;
  padding: 14px clamp(16px, 4vw, 32px);
  border-bottom: 1px solid rgba(255,255,255,.06);
  background: linear-gradient(to bottom, rgba(255,255,255,.02), transparent);
  backdrop-filter: blur(6px);
  position: sticky; top: 0; z-index: 5;
}
.brand { display: flex; align-items: center; gap: 12px; }
.brand h1 { font-size: clamp(18px, 2vw, 22px); margin: 0; letter-spacing: 0.02em; }
.seal {
  display:inline-grid; place-items:center;
  width: 32px; height: 32px; border-radius: 50%;
  background: radial-gradient(circle at 35% 30%, #ffe48a, #f0b544);
  color: #2c1a00; font-weight: 700; font-size: 14px;
  box-shadow: 0 0 12px rgba(246,200,92,.45);
}

.controls { display: flex; align-items: center; gap: 12px; flex-wrap: wrap; }
.ctrl { display: inline-flex; align-items: center; gap: 8px; color: var(--muted); font-size: 14px; }
.ctrl input[type="range"] { width: 140px; }
.toggle { gap: 6px; }
.share {
  border: 1px solid rgba(255,255,255,.12);
  background: transparent; color: var(--ink);
  padding: 8px 12px; border-radius: 999px; cursor: pointer;
}
.share:hover { border-color: rgba(255,255,255,.25); }

/* 히어로 */
.hero {
  position: relative; overflow: hidden;
  min-height: 62vh;
  display: grid; place-items: center;
  background:
    radial-gradient(1200px 800px at 50% -10%, rgba(62,130,255,.08), transparent 60%),
    radial-gradient(800px 600px at 30% -5%, rgba(251,211,141,.08), transparent 60%),
    linear-gradient(to bottom, var(--bg2), var(--bg));
}

.hanji {
  position: absolute; inset: 0; opacity: .07; pointer-events: none;
  background:
    repeating-linear-gradient(0deg, #fff, #fff 1px, transparent 1px, transparent 7px),
    repeating-linear-gradient(90deg, #fff, #fff 1px, transparent 1px, transparent 7px);
  mix-blend-mode: overlay;
}

.moon {
  position: absolute; top: 42%; left: 50%;
  width: clamp(220px, 40vw, 420px); height: auto;
  filter: drop-shadow(0 16px 60px rgba(246,200,92,.35));
  transition: transform .25s ease;
  will-change: transform;
}

/* 학 애니메이션 */
.crane {
  position: absolute; width: 120px; opacity: .65; filter: drop-shadow(0 0 6px rgba(255,255,255,.12));
  animation: fly 24s linear infinite;
}
.c1 { top: 20%; left: -15%; animation-delay: 0s; }
.c2 { top: 28%; left: -25%; transform: scale(.9); animation-delay: 8s; }

@keyframes fly {
  0%   { transform: translateX(0) translateY(0) }
  50%  { transform: translateX(70vw) translateY(-3vh) }
  100% { transform: translateX(110vw) translateY(2vh) }
}

/* 등불 */
.lantern {
  position: absolute; top: -6px; width: 120px; height: 220px;
  transform-origin: top center;
  animation: swing 4.8s ease-in-out infinite alternate;
}
.lantern.left  { left: clamp(8px, 8vw, 80px); }
.lantern.right { right: clamp(8px, 8vw, 80px); animation-delay: .8s; }
@keyframes swing { from { rotate: -4deg } to { rotate: 4deg } }

.lantern .rope { width: 2px; height: 60px; background: rgba(255,255,255,.4); margin: 0 auto; }
.lantern .body {
  margin: 0 auto; width: 110px; height: 120px; border-radius: 18px;
  background: radial-gradient(120% 100% at 50% 0%, #ffd984 0%, #f0b544 60%, #c98a2b 100%);
  box-shadow: 0 10px 30px rgba(247,203,109,.35), inset 0 -10px 18px rgba(0,0,0,.25);
  position: relative;
}
.lantern .rib {
  position: absolute; left: 8px; right: 8px; height: 2px; background: rgba(0,0,0,.18);
}
.lantern .rib:nth-child(1) { top: 28px; }
.lantern .rib:nth-child(2) { top: 58px; }
.lantern .rib:nth-child(3) { top: 88px; }
.lantern .tassel {
  width: 6px; height: 32px; background: linear-gradient(#c98a2b, #8a5d1b);
  margin: 6px auto 0; border-radius: 0 0 8px 8px;
}

/* 소원 */
.wish {
  position: relative; z-index: 1; margin-top: 36vh; text-align: center;
  transform: translateY(-50%);
}
.wish-text {
  font-size: clamp(20px, 3.2vw, 34px);
  line-height: 1.25;
  background: linear-gradient(90deg, white 0%, black 50%, white 100%);
  -webkit-background-clip: text; background-clip: text; color: transparent;
  text-shadow: 0 2px 14px rgba(255, 228, 138, .15);
}
.wish-actions { margin-top: 14px; display: flex; gap: 10px; justify-content: center; }
.btn {
  padding: 10px 14px; border-radius: 999px; cursor: pointer;
  border: 1px solid black;
  background: black; color: var(--ink);
  backdrop-filter: blur(6px);
}
.btn:hover { border-color: white; }
.btn.outline { background: transparent; }

/* 반짝이 */
.spark {
  position: absolute; width: 8px; height: 8px; border-radius: 50%;
  background: radial-gradient(circle, #fff, #ffe48a 60%, transparent 70%);
  animation: pop 2s ease-out forwards;
  pointer-events: none;
}
@keyframes pop {
  0% { transform: scale(.4); opacity: 0 }
  15% { transform: scale(1.2); opacity: 1 }
  100% { transform: scale(.9) translateY(-30px); opacity: 0 }
}

/* 카드 */
.cards {
  display: grid; gap: 14px; padding: 28px clamp(16px, 4vw, 40px);
  grid-template-columns: repeat(3, minmax(0, 1fr));
}
@media (max-width: 900px) { .cards { grid-template-columns: 1fr; } }

.card {
  background: linear-gradient(180deg, rgba(255,255,255,.03), rgba(255,255,255,.01));
  border: 1px solid rgba(255,255,255,.06);
  border-radius: 16px; padding: 18px 20px;
  box-shadow: 0 8px 28px rgba(0,0,0,.25);
}
.card h3 { margin: 0 0 8px; font-size: 18px; color: var(--accent); }
.card p { margin: 0; color: var(--muted); }

/* 푸터 */
.footer { text-align: center; color: var(--muted); padding: 20px; border-top: 1px solid rgba(255,255,255,.06); }

/* 모션 최소화 사용자 배려 */
@media (prefers-reduced-motion: reduce) {
  .crane, .lantern { animation: none !important; }
}
</style>