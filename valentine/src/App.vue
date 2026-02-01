<script setup>
import { ref, reactive, onMounted, onUnmounted, computed, watch } from "vue";

// --- Data ---
const title = ref("💕 Hey Beautiful 💕");
const question = ref("Will you be my Valentine?");
const yesText = ref("Yes! 💖");
const noText = ref("No");
const answered = ref(false);
const celebrationTitle = ref("🎉 Yay! You said YES! 🎉");
const celebrationMessage = ref(
  "You just made me the happiest person in the world! I promise to make this Valentine's Day unforgettable. ❤️",
);
const customMessage = ref("");
const noClickCount = ref(0);
const noButtonStyle = ref({});
const backgroundHearts = ref([]);
const floatingHearts = ref([]);
const floatingPetals = ref([]);
const heartCount = ref(0);
const showCounter = ref(false);
const sprinkles = ref([]);
const confettiPieces = ref([]);
const musicPlaying = ref(false);
const timeLeft = reactive({
  days: 0,
  hours: 0,
  minutes: 0,
  seconds: 0,
});
const currentQuote = ref("");
const submitting = ref(false);
const messageSent = ref(false);
const showLoveNotes = ref(false);
const currentNoteIndex = ref(0);

// --- User Settings ---
const userEmail = ref("philipnicholas386@gmail.com");
const userPhoneNumber = ref("0593870678");

// --- Media Sources ---
const videoSource = ref(
  "https://www.shutterstock.com/shutterstock/videos/3466278135/preview/stock-footage-man-blowing-a-kiss-while-looking-at-the-camera.webm",
);

// Elvis Presley - Can't Help Falling in Love (YouTube Audio)
const youtubeVideoId = ref("vGJTaP6anOU"); // Official audio

const quotes = [
  "Out of everyone around, you somehow stand out to me.",
  "I enjoy talking to you more than I expected to.",
  "You have a way of making moments feel lighter.",
  "Being around you just feels… easy.",
  "I catch myself smiling whenever you cross my mind.",
  "Hey, crazy… how did you quietly become this interesting? 😄",
  "Hey, carry me small… you’re doing too much already 😂",
  "I think I’m being too into you o lol 😅",
];

const reasons = [
  "I genuinely enjoy your vibe",
  "You make conversations fun and effortless",
  "I like the way you think",
  "You bring good energy around me",
  "Spending time with you feels natural",
  "I’m curious about where this could go",
  "No pressure, but I think I’m catching feelings small small 😌",
  "Hopefully, we get to see where this leads",
  "I like the idea of us becoming something, one day",
  "With you, things feel fun — not forced",
];

const loveNotes = [
  { emoji: "🌹", text: "You're the rose in my garden of life" },
  { emoji: "⭐", text: "You shine brighter than all the stars" },
  { emoji: "🎵", text: "You're the melody to my heart's song" },
  { emoji: "🌈", text: "You bring color to my world" },
  { emoji: "🦋", text: "You give me butterflies every day" },
  { emoji: "☀️", text: "You're my sunshine on cloudy days" },
];

const photos = [
  {
    url: "https://images.unsplash.com/photo-1518199266791-5375a83190b7?w=300&h=300&fit=crop",
    rotation: "-5deg",
    caption: "Beautiful moments",
  },
  {
    url: "https://images.unsplash.com/photo-1529333166437-7750a6dd5a70?w=300&h=300&fit=crop",
    rotation: "3deg",
    caption: "Love in bloom",
  },
  {
    url: "https://images.unsplash.com/photo-1516589178581-6cd7833ae3b2?w=300&h=300&fit=crop",
    rotation: "-3deg",
    caption: "Together forever",
  },
  {
    url: "https://images.unsplash.com/photo-1496181133206-80ce9b88a853?w=300&h=300&fit=crop",
    rotation: "5deg",
    caption: "Our story",
  },
];

let heartIdCounter = 0;
let sparkleIdCounter = 0;
let confettiIdCounter = 0;
let quoteInterval = null;
let countdownInterval = null;
let noteInterval = null;

// Computed property for yes button scale
const yesButtonScale = computed(() => {
  return 1 + noClickCount.value * 0.15;
});

const createBackgroundHearts = () => {
  const emojis = ["💕", "💖", "💗", "💝", "💘", "❤️", "🌹", "✨"];
  for (let i = 0; i < 25; i++) {
    backgroundHearts.value.push({
      id: i,
      emoji: emojis[Math.floor(Math.random() * emojis.length)],
      style: {
        left: Math.random() * 100 + "%",
        top: Math.random() * 100 + "%",
        animationDelay: Math.random() * 5 + "s",
        fontSize: Math.random() * 2 + 0.8 + "rem",
        opacity: Math.random() * 0.4 + 0.1,
      },
    });
  }
};

const createFloatingPetals = () => {
  const flowerEmojis = ["🌸", "🌹", "🌷", "🌺", "🌼", "🏵️"];
  setInterval(() => {
    if (floatingPetals.value.length > 15) return;

    const id = Date.now() + Math.random();
    floatingPetals.value.push({
      id,
      emoji: flowerEmojis[Math.floor(Math.random() * flowerEmojis.length)],
      style: {
        left: Math.random() * 100 + "%",
        top: "-50px",
        fontSize: Math.random() * 1.5 + 1 + "rem",
        animationDuration: Math.random() * 10 + 12 + "s",
        transform: `rotate(${Math.random() * 360}deg)`,
        opacity: Math.random() * 0.6 + 0.3,
      },
    });

    setTimeout(() => {
      const index = floatingPetals.value.findIndex((p) => p.id === id);
      if (index > -1) floatingPetals.value.splice(index, 1);
    }, 22000);
  }, 1200);
};

const createFloatingHearts = (count) => {
  for (let i = 0; i < count; i++) {
    setTimeout(() => {
      const id = heartIdCounter++;
      const emojis = ["❤️", "💕", "💖", "💗", "💘"];
      const heart = {
        id,
        emoji: emojis[Math.floor(Math.random() * emojis.length)],
        style: {
          left: Math.random() * 100 + "%",
          bottom: "-50px",
          animationDelay: Math.random() * 0.5 + "s",
          fontSize: Math.random() * 2.5 + 1.5 + "rem",
        },
      };
      floatingHearts.value.push(heart);
      heartCount.value++;

      setTimeout(() => {
        const index = floatingHearts.value.findIndex((h) => h.id === id);
        if (index > -1) {
          floatingHearts.value.splice(index, 1);
        }
      }, 4000);
    }, i * 80);
  }
};

const launchConfetti = () => {
  const colors = [
    "#ff4d6d",
    "#ff758f",
    "#ff85a1",
    "#f7cad0",
    "#ffc6d9",
    "#ffb3c1",
    "#ffffff",
    "#ffe5ec",
  ];
  for (let i = 0; i < 80; i++) {
    setTimeout(() => {
      const id = confettiIdCounter++;
      const confetti = {
        id,
        style: {
          left: Math.random() * 100 + "%",
          top: "-20px",
          "--color": colors[Math.floor(Math.random() * colors.length)],
          animationDelay: Math.random() * 0.5 + "s",
          animationDuration: Math.random() * 3 + 2.5 + "s",
          transform: `rotate(${Math.random() * 360}deg)`,
          width: Math.random() * 8 + 6 + "px",
          height: Math.random() * 8 + 6 + "px",
        },
      };
      confettiPieces.value.push(confetti);

      setTimeout(() => {
        const index = confettiPieces.value.findIndex((c) => c.id === id);
        if (index > -1) {
          confettiPieces.value.splice(index, 1);
        }
      }, 6000);
    }, i * 30);
  }
};

const createSparkles = (event) => {
  const emojis = ["✨", "💖", "🌸", "🌟", "⭐", "💫"];
  for (let i = 0; i < 12; i++) {
    const angle = (Math.PI * 2 * i) / 12;
    const distance = 70;
    const tx = Math.cos(angle) * distance;
    const ty = Math.sin(angle) * distance;

    const id = sparkleIdCounter++;
    const sparkle = {
      id,
      emoji: emojis[Math.floor(Math.random() * emojis.length)],
      style: {
        left: event.clientX + "px",
        top: event.clientY + "px",
        "--tx": tx + "px",
        "--ty": ty + "px",
      },
    };
    sparkles.value.push(sparkle);

    setTimeout(() => {
      const index = sparkles.value.findIndex((s) => s.id === id);
      if (index > -1) {
        sparkles.value.splice(index, 1);
      }
    }, 1200);
  }
};

const sayYes = () => {
  answered.value = true;
  showCounter.value = true;
  musicPlaying.value = true;

  // Play YouTube audio via iframe API
  if (window.YT && window.YT.Player) {
    const player = new window.YT.Player("youtube-player", {
      events: {
        onReady: (event) => {
          event.target.playVideo();
        },
      },
    });
  }

  createFloatingHearts(50);
  launchConfetti();
  startCountdown();

  // Continuous heart animation
  setInterval(() => {
    createFloatingHearts(5);
  }, 1500);

  // Periodic confetti bursts
  setInterval(() => {
    launchConfetti();
  }, 15000);

  // Start love notes rotation
  showLoveNotes.value = true;
  noteInterval = setInterval(() => {
    currentNoteIndex.value = (currentNoteIndex.value + 1) % loveNotes.length;
  }, 4000);
};

const onYesHover = () => {
  createFloatingHearts(4);
};

const noClicked = () => {
  noClickCount.value++;
  updateYesText();
  moveNoButton();
  createFloatingHearts(2);
};

const moveNoButton = () => {
  const maxX = window.innerWidth - 150;
  const maxY = window.innerHeight - 80;
  const randomX = Math.random() * maxX;
  const randomY = Math.random() * maxY;

  noButtonStyle.value = {
    position: "fixed",
    left: randomX + "px",
    top: randomY + "px",
    transition: "all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275)",
    zIndex: 1000,
  };
};

const getNoMessage = () => {
  const messages = [
    "Are you sure? 🥺",
    "Think again... 💕",
    "I'll be so sad! 😭",
    "Give me a chance! ✨",
    "The 'Yes' button looks better! 💖",
    "You're breaking my heart... 💔",
    "Wait! Don't click that! 🛑",
    "Pretty please? 🥹",
    "Look how big the Yes button is now! 👀",
    "My heart can't take this... 💘",
  ];
  return messages[Math.min(noClickCount.value - 1, messages.length - 1)];
};

const updateYesText = () => {
  const texts = [
    "Yes! 💖",
    "YES!! 💕",
    "YESSS!!! 💗",
    "Definitely YES! 💝",
    "Absolutely 100% Yes! 💖",
    "YES PLEASE! 💘",
  ];
  yesText.value = texts[Math.min(noClickCount.value, texts.length - 1)];
};

const toggleMusic = () => {
  musicPlaying.value = !musicPlaying.value;

  const iframe = document.getElementById("youtube-player");
  if (iframe && iframe.contentWindow) {
    if (musicPlaying.value) {
      iframe.contentWindow.postMessage(
        '{"event":"command","func":"playVideo","args":""}',
        "*",
      );
      createFloatingHearts(10);
    } else {
      iframe.contentWindow.postMessage(
        '{"event":"command","func":"pauseVideo","args":""}',
        "*",
      );
    }
  }
};

const updateCountdown = () => {
  const valentinesDay = new Date("2026-02-14T00:00:00");
  const now = new Date();
  const diff = valentinesDay - now;

  if (diff > 0) {
    timeLeft.days = Math.floor(diff / (1000 * 60 * 60 * 24));
    timeLeft.hours = Math.floor(
      (diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60),
    );
    timeLeft.minutes = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60));
    timeLeft.seconds = Math.floor((diff % (1000 * 60)) / 1000);
  } else {
    timeLeft.days = 0;
    timeLeft.hours = 0;
    timeLeft.minutes = 0;
    timeLeft.seconds = 0;
  }
};

const startCountdown = () => {
  updateCountdown();
  countdownInterval = setInterval(updateCountdown, 1000);
};

const rotateQuote = () => {
  const randomIndex = Math.floor(Math.random() * quotes.length);
  currentQuote.value = quotes[randomIndex];
};

const submitEmail = async () => {
  if (!customMessage.value || submitting.value) return;
  submitting.value = true;
  try {
    const formData = new FormData();
    formData.append("email", userEmail.value);
    formData.append("message", customMessage.value);
    formData.append("_subject", "💕 Valentine Response - She Said YES!");
    formData.append("_captcha", "false");
    await fetch(`https://formsubmit.co/${userEmail.value}`, {
      method: "POST",
      body: formData,
    });
    messageSent.value = true;
    createFloatingHearts(25);
    launchConfetti();
  } catch (error) {
    console.error("Error sending message:", error);
  } finally {
    submitting.value = false;
  }
};

const sendWhatsApp = () => {
  if (!customMessage.value) return;
  const text = encodeURIComponent(
    `💕 Valentine Response! 💕\n\nI said YES!\n\nMessage: ${customMessage.value}`,
  );
  window.open(`https://wa.me/${userPhoneNumber.value}?text=${text}`, "_blank");
  messageSent.value = true;
  customMessage.value = "";
  createFloatingHearts(15);
};

onMounted(() => {
  createBackgroundHearts();
  createFloatingPetals();
  rotateQuote();
  quoteInterval = setInterval(rotateQuote, 7000);
});

onUnmounted(() => {
  if (quoteInterval) clearInterval(quoteInterval);
  if (countdownInterval) clearInterval(countdownInterval);
  if (noteInterval) clearInterval(noteInterval);
});
</script>

<template>
  <div id="app-container">
    <div class="overlay"></div>

    <transition name="fade">
      <div class="love-counter" v-if="showCounter">
        💕 {{ heartCount }} Hearts Gathered
      </div>
    </transition>

    <div class="music-player" v-if="answered">
      <button
        class="music-btn"
        @click="toggleMusic"
        :class="{ 'is-playing': musicPlaying }"
        :title="musicPlaying ? 'Pause music' : 'Play music'"
      >
        <span v-if="musicPlaying">🔊</span>
        <span v-else>🔇</span>
      </button>
    </div>

    <!-- Hidden YouTube Player for Background Music -->
    <div v-if="answered" style="position: absolute; left: -9999px">
      <iframe
        id="youtube-player"
        width="0"
        height="0"
        :src="`https://www.youtube.com/embed/${youtubeVideoId}?enablejsapi=1&autoplay=1&loop=1&playlist=${youtubeVideoId}&controls=0`"
        frameborder="0"
        allow="autoplay; encrypted-media"
      ></iframe>
    </div>

    <transition name="fade">
      <div v-if="showLoveNotes" class="love-note-display">
        <transition name="slide-fade" mode="out-in">
          <div :key="currentNoteIndex" class="love-note">
            <span class="note-emoji">{{
              loveNotes[currentNoteIndex].emoji
            }}</span>
            <p>{{ loveNotes[currentNoteIndex].text }}</p>
          </div>
        </transition>
      </div>
    </transition>

    <div class="container">
      <transition name="fade-scale" mode="out-in">
        <div v-if="!answered" class="main-card glass" key="ask">
          <div class="card-content">
            <h1 class="title">{{ title }}</h1>
            <p class="question">{{ question }}</p>

            <div class="quote-box">
              <transition name="fade" mode="out-in">
                <div :key="currentQuote" class="quote-content">
                  <span class="quote-mark">"</span>
                  {{ currentQuote }}
                  <span class="quote-mark">"</span>
                </div>
              </transition>
            </div>

            <div class="buttons">
              <button
                class="yes-btn"
                @click="sayYes"
                @mouseenter="onYesHover"
                :style="{ transform: `scale(${yesButtonScale})` }"
              >
                {{ yesText }}
              </button>
              <button
                class="no-btn"
                @click="noClicked"
                @mouseenter="moveNoButton"
                :style="noButtonStyle"
              >
                {{ noText }}
              </button>
            </div>

            <transition name="bounce">
              <p v-if="noClickCount > 0" class="no-message">
                {{ getNoMessage() }}
              </p>
            </transition>

            <div class="reasons-list">
              <h3>Why You Mean So Much <span class="pulse-heart">✨</span></h3>
              <div class="reasons-grid">
                <div
                  v-for="(reason, index) in reasons"
                  :key="index"
                  class="reason-item"
                  :style="{ animationDelay: index * 0.1 + 's' }"
                >
                  {{ reason }}
                </div>
              </div>
            </div>
          </div>
        </div>

        <div v-else class="celebration-card glass" key="celebrate">
          <div class="card-content">
            <h2 class="celebration-title">{{ celebrationTitle }}</h2>
            <p class="celebration-msg">{{ celebrationMessage }}</p>

            <div class="video-container">
              <div class="video-wrapper">
                <video
                  class="video-element"
                  autoplay
                  loop
                  muted
                  playsinline
                  :src="videoSource"
                >
                  Your browser does not support the video tag.
                </video>
                <div class="video-overlay"></div>
              </div>
            </div>

            <div class="countdown-section">
              <h3>The Big Day Countdown <span class="pulse-heart">❤️</span></h3>
              <div class="time-units">
                <div class="time-unit">
                  <span class="number">{{ timeLeft.days }}</span>
                  <span class="label">Days</span>
                </div>
                <div class="time-unit">
                  <span class="number">{{ timeLeft.hours }}</span>
                  <span class="label">Hrs</span>
                </div>
                <div class="time-unit">
                  <span class="number">{{ timeLeft.minutes }}</span>
                  <span class="label">Min</span>
                </div>
                <div class="time-unit">
                  <span class="number">{{ timeLeft.seconds }}</span>
                  <span class="label">Sec</span>
                </div>
              </div>
            </div>

            <div class="message-form">
              <h3 class="form-title">Send Me a Love Letter 💕</h3>
              <textarea
                v-model="customMessage"
                placeholder="What's in your heart?..."
                maxlength="500"
                @focus="createFloatingHearts(3)"
                @input="messageSent = false"
              ></textarea>
              <div class="char-counter">{{ customMessage.length }}/500</div>

              <div class="submission-actions">
                <button
                  class="submit-btn main-action"
                  @click="sendWhatsApp"
                  :disabled="!customMessage"
                >
                  <span class="btn-icon">�</span>
                  Send My Note
                </button>
              </div>

              <transition name="bounce">
                <p v-if="messageSent" class="success-pop">
                  ✨ Message delivered with love! ✨
                </p>
              </transition>
            </div>

            <div class="photo-memories">
              <h3 class="memories-title">Our Moments Together</h3>
              <div class="photo-grid">
                <div
                  v-for="(photo, index) in photos"
                  :key="index"
                  class="photo-item"
                  :style="{
                    '--rotation': photo.rotation,
                    animationDelay: index * 0.15 + 's',
                  }"
                  @click="createSparkles($event)"
                >
                  <img :src="photo.url" alt="Memory" loading="lazy" />
                  <div class="photo-caption">{{ photo.caption }}</div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </transition>
    </div>

    <!-- Background Elements -->
    <div
      v-for="heart in backgroundHearts"
      :key="heart.id"
      class="bg-heart"
      :style="heart.style"
    >
      {{ heart.emoji }}
    </div>

    <div
      v-for="petal in floatingPetals"
      :key="petal.id"
      class="floating-petal"
      :style="petal.style"
    >
      {{ petal.emoji }}
    </div>

    <div
      v-for="heart in floatingHearts"
      :key="heart.id"
      class="floating-heart"
      :style="heart.style"
    >
      {{ heart.emoji }}
    </div>

    <div
      v-for="sparkle in sparkles"
      :key="sparkle.id"
      class="sparkle-particle"
      :style="sparkle.style"
    >
      {{ sparkle.emoji }}
    </div>

    <div
      v-for="confetti in confettiPieces"
      :key="confetti.id"
      class="confetti"
      :style="confetti.style"
    ></div>
  </div>
</template>

<style>
@import url("https://fonts.googleapis.com/css2?family=Dancing+Script:wght@700&family=Outfit:wght@300;400;600;700&display=swap");

:root {
  --primary: #ff4d6d;
  --secondary: #ff85a1;
  --accent: #ffc6d9;
  --dark: #590d22;
  --glass: rgba(255, 255, 255, 0.12);
  --glass-border: rgba(255, 255, 255, 0.2);
}

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: "Outfit", sans-serif;
  overflow: hidden;
}

#app-container {
  min-height: 100vh;
  width: 100vw;
  background: radial-gradient(
    circle at top right,
    #590d22,
    #800f2f,
    #a4133c,
    #c9184a,
    #ff4d6d
  );
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  overflow: hidden;
  color: white;
}

.overlay {
  position: absolute;
  inset: 0;
  background: url("data:image/svg+xml,%3Csvg width='60' height='60' viewBox='0 0 60 60' xmlns='http://www.w3.org/2000/svg'%3E%3Cg fill='none' fill-rule='evenodd'%3E%3Cg fill='%23ffffff' fill-opacity='0.05'%3E%3Cpath d='M36 34v-4h-2v4h-4v2h4v4h2v-4h4v-2h-4zm0-30V0h-2v4h-4v2h4v4h2V6h4V4h-4zM6 34v-4H4v4H0v2h4v4h2v-4h4v-2H6zM6 4V0H4v4H0v2h4v4h2V6h4V4H6z'/%3E%3C/g%3E%3C/g%3E%3C/svg%3E");
  opacity: 0.3;
  pointer-events: none;
}

.glass {
  background: var(--glass);
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  border: 1px solid var(--glass-border);
  border-radius: 24px;
  box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.37);
}

.container {
  width: 100%;
  max-width: 700px;
  padding: 20px;
  z-index: 10;
  perspective: 1000px;
}

.main-card,
.celebration-card {
  padding: 50px 40px;
  text-align: center;
  position: relative;
  overflow-y: auto;
  max-height: 90vh;
}

.title {
  font-family: "Dancing Script", cursive;
  font-size: 4.5rem;
  margin-bottom: 10px;
  background: linear-gradient(135deg, #ffffff, #ffb3c1, #ff85a1);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  filter: drop-shadow(0 2px 8px rgba(255, 255, 255, 0.3));
  animation: titleGlow 3s ease-in-out infinite;
}

@keyframes titleGlow {
  0%,
  100% {
    filter: drop-shadow(0 2px 8px rgba(255, 255, 255, 0.3));
  }
  50% {
    filter: drop-shadow(0 4px 16px rgba(255, 133, 161, 0.6));
  }
}

.question {
  font-size: 2rem;
  font-weight: 300;
  margin-bottom: 35px;
  color: #ffcad4;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.quote-box {
  background: rgba(0, 0, 0, 0.25);
  padding: 25px;
  border-radius: 16px;
  font-style: italic;
  font-size: 1.25rem;
  margin-bottom: 40px;
  position: relative;
  border-left: 4px solid var(--primary);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
  min-height: 100px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.quote-content {
  line-height: 1.6;
}

.quote-mark {
  color: var(--primary);
  font-size: 2.5rem;
  font-weight: bold;
  opacity: 0.6;
}

.buttons {
  display: flex;
  gap: 20px;
  justify-content: center;
  margin-bottom: 30px;
  flex-wrap: wrap;
}

button {
  cursor: pointer;
  border: none;
  font-family: inherit;
  font-weight: 600;
  transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}

.yes-btn {
  padding: 18px 50px;
  font-size: 1.5rem;
  background: linear-gradient(135deg, #ff4d6d, #ff758f, #ff4d6d);
  background-size: 200% 200%;
  color: white;
  border-radius: 50px;
  box-shadow: 0 0 20px rgba(255, 77, 109, 0.5);
  animation:
    pulse 2s infinite,
    gradientShift 3s ease infinite;
  position: relative;
  overflow: hidden;
}

@keyframes gradientShift {
  0%,
  100% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
}

.yes-btn::before {
  content: "";
  position: absolute;
  top: 50%;
  left: 50%;
  width: 0;
  height: 0;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.3);
  transform: translate(-50%, -50%);
  transition:
    width 0.6s,
    height 0.6s;
}

.yes-btn:hover::before {
  width: 300px;
  height: 300px;
}

.yes-btn:hover {
  transform: scale(1.1) translateY(-5px);
  box-shadow: 0 15px 40px rgba(255, 77, 109, 0.8);
}

.no-btn {
  padding: 15px 40px;
  font-size: 1.2rem;
  background: rgba(255, 255, 255, 0.08);
  color: #ddd;
  border-radius: 50px;
  border: 1px solid rgba(255, 255, 255, 0.2);
}

.no-btn:hover {
  background: rgba(255, 255, 255, 0.05);
}

.no-message {
  color: #ffcad4;
  margin-bottom: 25px;
  font-size: 1.2rem;
  font-weight: 500;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.reasons-list {
  margin-top: 40px;
}

.reasons-list h3 {
  font-family: "Dancing Script", cursive;
  font-size: 2.2rem;
  margin-bottom: 25px;
  color: #ffb3c1;
}

.reasons-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 15px;
  text-align: left;
}

.reason-item {
  background: rgba(255, 255, 255, 0.08);
  padding: 15px 20px;
  border-radius: 14px;
  font-size: 1rem;
  display: flex;
  align-items: center;
  animation: slideIn 0.6s ease-out forwards;
  opacity: 0;
  border: 1px solid rgba(255, 255, 255, 0.1);
  transition: all 0.3s ease;
}

.reason-item:hover {
  background: rgba(255, 255, 255, 0.12);
  transform: translateX(5px);
  border-color: var(--secondary);
}

.reason-item::before {
  content: "💖";
  margin-right: 12px;
  font-size: 1.2rem;
}

/* Celebration Styles */
.celebration-title {
  font-family: "Dancing Script", cursive;
  font-size: 4rem;
  margin-bottom: 15px;
  color: #fff;
  animation: celebrationBounce 1s ease-out;
}

@keyframes celebrationBounce {
  0% {
    transform: scale(0.3) rotate(-10deg);
    opacity: 0;
  }
  50% {
    transform: scale(1.1) rotate(5deg);
  }
  100% {
    transform: scale(1) rotate(0deg);
    opacity: 1;
  }
}

.celebration-msg {
  font-size: 1.3rem;
  color: #ffcad4;
  margin-bottom: 35px;
  line-height: 1.6;
}

.video-container {
  margin: 35px 0;
  border-radius: 24px;
  overflow: hidden;
  box-shadow: 0 20px 50px rgba(0, 0, 0, 0.5);
  background: #000;
  border: 2px solid rgba(255, 255, 255, 0.1);
}

.video-wrapper {
  position: relative;
  width: 100%;
}

.video-element {
  width: 100%;
  height: auto;
  max-height: 70vh;
  object-fit: contain;
  display: block;
}

.video-overlay {
  position: absolute;
  inset: 0;
  background: linear-gradient(to top, rgba(0, 0, 0, 0.5), transparent);
  pointer-events: none;
}

.countdown-section {
  margin: 45px 0;
}

.countdown-section h3 {
  font-family: "Dancing Script", cursive;
  font-size: 2rem;
  margin-bottom: 20px;
  color: #ffb3c1;
}

.time-units {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin-top: 20px;
  flex-wrap: wrap;
}

.time-unit {
  background: rgba(255, 255, 255, 0.1);
  padding: 20px;
  border-radius: 18px;
  min-width: 90px;
  display: flex;
  flex-direction: column;
  border: 1px solid rgba(255, 255, 255, 0.15);
  transition: all 0.3s ease;
}

.time-unit:hover {
  background: rgba(255, 255, 255, 0.15);
  transform: translateY(-5px);
  box-shadow: 0 8px 20px rgba(255, 77, 109, 0.3);
}

.time-unit .number {
  font-size: 2.5rem;
  font-weight: 700;
  color: #ff758f;
  text-shadow: 0 2px 8px rgba(255, 117, 143, 0.5);
}

.time-unit .label {
  font-size: 0.85rem;
  text-transform: uppercase;
  color: #ffb3c1;
  letter-spacing: 1px;
  margin-top: 5px;
}

.message-form {
  background: rgba(0, 0, 0, 0.2);
  padding: 30px;
  border-radius: 24px;
  margin: 45px 0;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.form-title {
  font-family: "Dancing Script", cursive;
  font-size: 2rem;
  margin-bottom: 20px;
  color: #ffb3c1;
}

textarea {
  width: 100%;
  height: 140px;
  background: rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.2);
  border-radius: 14px;
  padding: 18px;
  color: white;
  font-family: inherit;
  font-size: 1rem;
  margin-bottom: 10px;
  resize: none;
  transition: all 0.3s ease;
}

textarea:focus {
  outline: none;
  border-color: var(--primary);
  background: rgba(255, 255, 255, 0.15);
  box-shadow: 0 0 20px rgba(255, 77, 109, 0.3);
}

textarea::placeholder {
  color: rgba(255, 255, 255, 0.5);
}

.char-counter {
  text-align: right;
  font-size: 0.85rem;
  color: rgba(255, 255, 255, 0.5);
  margin-bottom: 20px;
}

.submission-actions {
  display: flex;
  justify-content: center;
  margin-top: 10px;
}

.submit-btn {
  padding: 14px;
  border-radius: 14px;
  font-size: 1.05rem;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
}

.btn-icon {
  font-size: 1.2rem;
}

.main-action {
  background: linear-gradient(135deg, #ff4d6d, #ff85a1);
  color: white;
  box-shadow: 0 4px 15px rgba(255, 77, 109, 0.3);
  width: 100%;
  max-width: 350px;
}

.main-action:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(255, 77, 109, 0.5);
}

.submit-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
  transform: none;
}

.success-pop {
  margin-top: 20px;
  font-size: 1.2rem;
  color: #90ee90;
  font-weight: 600;
  text-shadow: 0 2px 8px rgba(144, 238, 144, 0.5);
}

.photo-memories {
  margin-top: 50px;
}

.memories-title {
  font-family: "Dancing Script", cursive;
  font-size: 2.2rem;
  margin-bottom: 30px;
  color: #ffb3c1;
}

.photo-grid {
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  gap: 25px;
}

.photo-item {
  width: 140px;
  height: 140px;
  padding: 10px;
  background: white;
  box-shadow: 0 12px 25px rgba(0, 0, 0, 0.3);
  transform: rotate(var(--rotation));
  transition: transform 0.4s;
  cursor: pointer;
  position: relative;
  animation: photoFloat 0.6s ease-out;
  opacity: 0;
  animation-fill-mode: forwards;
}

@keyframes photoFloat {
  from {
    opacity: 0;
    transform: translateY(30px) rotate(var(--rotation));
  }
  to {
    opacity: 1;
    transform: translateY(0) rotate(var(--rotation));
  }
}

.photo-item:hover {
  transform: scale(1.15) rotate(0deg);
  z-index: 10;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.5);
}

.photo-item img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.photo-caption {
  position: absolute;
  bottom: -30px;
  left: 50%;
  transform: translateX(-50%);
  background: rgba(0, 0, 0, 0.7);
  color: white;
  padding: 5px 12px;
  border-radius: 12px;
  font-size: 0.75rem;
  white-space: nowrap;
  opacity: 0;
  transition: opacity 0.3s;
  pointer-events: none;
}

.photo-item:hover .photo-caption {
  opacity: 1;
}

/* UI Utilities */
.music-player {
  position: fixed;
  top: 20px;
  right: 20px;
  z-index: 100;
}

.music-btn {
  width: 55px;
  height: 55px;
  border-radius: 50%;
  background: var(--glass);
  backdrop-filter: blur(10px);
  border: 1px solid var(--glass-border);
  font-size: 1.6rem;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
}

.music-btn:hover {
  background: rgba(255, 255, 255, 0.2);
  transform: scale(1.1);
}

.music-btn.is-playing {
  animation: musicPulse 2s ease-in-out infinite;
  box-shadow: 0 0 20px var(--primary);
}

@keyframes musicPulse {
  0%,
  100% {
    box-shadow: 0 0 20px rgba(255, 77, 109, 0.5);
  }
  50% {
    box-shadow: 0 0 30px rgba(255, 77, 109, 0.8);
  }
}

.love-counter {
  position: fixed;
  top: 20px;
  left: 20px;
  padding: 12px 24px;
  border-radius: 50px;
  background: var(--glass);
  backdrop-filter: blur(10px);
  font-weight: 600;
  z-index: 100;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
  border: 1px solid var(--glass-border);
}

.love-note-display {
  position: fixed;
  bottom: 30px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 100;
  max-width: 400px;
  width: 90%;
}

.love-note {
  background: var(--glass);
  backdrop-filter: blur(15px);
  padding: 20px 30px;
  border-radius: 20px;
  border: 1px solid var(--glass-border);
  text-align: center;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.3);
}

.note-emoji {
  font-size: 2rem;
  display: block;
  margin-bottom: 10px;
}

.love-note p {
  font-size: 1.1rem;
  color: #ffcad4;
  font-style: italic;
}

/* Background Animations */
.bg-heart {
  position: absolute;
  pointer-events: none;
  animation: floatBG 18s linear infinite;
  filter: drop-shadow(0 0 5px rgba(255, 255, 255, 0.3));
}

.floating-heart {
  position: absolute;
  animation: rise 4s ease-out forwards;
  filter: drop-shadow(0 0 8px rgba(255, 77, 109, 0.5));
}

.floating-petal {
  position: absolute;
  pointer-events: none;
  z-index: 1;
  animation: fallSway linear forwards;
}

.sparkle-particle {
  position: absolute;
  pointer-events: none;
  font-size: 1.3rem;
  animation: explode 1.2s ease-out forwards;
}

.confetti {
  position: absolute;
  background-color: var(--color);
  animation: fall var(--duration, 3s) linear forwards;
  border-radius: 2px;
}

/* Keyframes */
@keyframes pulse {
  0% {
    transform: scale(1);
    box-shadow: 0 0 10px rgba(255, 77, 109, 0.4);
  }
  50% {
    transform: scale(1.05);
    box-shadow: 0 0 30px rgba(255, 77, 109, 0.7);
  }
  100% {
    transform: scale(1);
    box-shadow: 0 0 10px rgba(255, 77, 109, 0.4);
  }
}

@keyframes floatBG {
  0% {
    transform: translateY(100vh) rotate(0deg);
  }
  100% {
    transform: translateY(-120vh) rotate(360deg);
  }
}

@keyframes rise {
  0% {
    opacity: 0;
    transform: translateY(0) scale(0.5);
  }
  20% {
    opacity: 1;
  }
  100% {
    opacity: 0;
    transform: translateY(-350px) scale(1.8);
  }
}

@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateX(-30px);
  }
  to {
    opacity: 1;
    transform: translateX(0);
  }
}

@keyframes explode {
  0% {
    transform: translate(0, 0) scale(1);
    opacity: 1;
  }
  100% {
    transform: translate(var(--tx), var(--ty)) scale(0);
    opacity: 0;
  }
}

@keyframes fall {
  0% {
    transform: translateY(0) rotate(0deg);
    opacity: 1;
  }
  100% {
    transform: translateY(110vh) rotate(720deg);
    opacity: 0.5;
  }
}

@keyframes fallSway {
  0% {
    transform: translateY(0) rotate(0deg) translateX(0);
  }
  25% {
    transform: translateY(25vh) rotate(90deg) translateX(50px);
  }
  50% {
    transform: translateY(50vh) rotate(180deg) translateX(-50px);
  }
  75% {
    transform: translateY(75vh) rotate(270deg) translateX(50px);
  }
  100% {
    transform: translateY(110vh) rotate(360deg) translateX(0);
  }
}

.pulse-heart {
  display: inline-block;
  animation: heartBeat 1.2s infinite;
}

@keyframes heartBeat {
  0%,
  100% {
    transform: scale(1);
  }
  15% {
    transform: scale(1.4);
  }
  30% {
    transform: scale(1);
  }
  45% {
    transform: scale(1.2);
  }
  60% {
    transform: scale(1);
  }
}

/* Vue Transitions */
.fade-scale-enter-active,
.fade-scale-leave-active {
  transition: all 0.7s cubic-bezier(0.4, 0, 0.2, 1);
}

.fade-scale-enter-from,
.fade-scale-leave-to {
  opacity: 0;
  transform: scale(0.85) translateY(30px);
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.slide-fade-enter-active,
.slide-fade-leave-active {
  transition: all 0.6s ease;
}

.slide-fade-enter-from {
  opacity: 0;
  transform: translateY(20px);
}

.slide-fade-leave-to {
  opacity: 0;
  transform: translateY(-20px);
}

.bounce-enter-active {
  animation: bounceIn 0.6s;
}

.bounce-leave-active {
  animation: bounceOut 0.4s;
}

@keyframes bounceIn {
  0% {
    opacity: 0;
    transform: scale(0.3);
  }
  50% {
    opacity: 1;
    transform: scale(1.1);
  }
  100% {
    transform: scale(1);
  }
}

@keyframes bounceOut {
  0% {
    transform: scale(1);
  }
  100% {
    opacity: 0;
    transform: scale(0.3);
  }
}

/* Scrollbar */
::-webkit-scrollbar {
  width: 8px;
}

::-webkit-scrollbar-track {
  background: transparent;
}

::-webkit-scrollbar-thumb {
  background: rgba(255, 255, 255, 0.25);
  border-radius: 10px;
}

::-webkit-scrollbar-thumb:hover {
  background: rgba(255, 255, 255, 0.35);
}

/* Responsive Design */
@media (max-width: 768px) {
  .title {
    font-size: 3rem;
  }

  .question {
    font-size: 1.5rem;
  }

  .quote-box {
    font-size: 1.1rem;
    padding: 20px;
  }

  .main-card,
  .celebration-card {
    padding: 30px 25px;
  }

  .reasons-grid {
    grid-template-columns: 1fr;
  }

  .time-units {
    gap: 12px;
  }

  .time-unit {
    min-width: 75px;
    padding: 15px;
  }

  .time-unit .number {
    font-size: 2rem;
  }

  .submission-actions {
    grid-template-columns: 1fr;
  }

  .photo-item {
    width: 110px;
    height: 110px;
  }

  .celebration-title {
    font-size: 3rem;
  }
}

@media (max-width: 480px) {
  .title {
    font-size: 2.5rem;
  }

  .yes-btn {
    padding: 15px 35px;
    font-size: 1.3rem;
  }

  .no-btn {
    padding: 12px 30px;
    font-size: 1.1rem;
  }

  .music-player {
    top: 10px;
    right: 10px;
  }

  .love-counter {
    top: 10px;
    left: 10px;
    font-size: 0.9rem;
    padding: 10px 18px;
  }
}
</style>
