<template>
  <div class="psalms-content">
    <!-- Snow container -->
    <div class="snow"></div>

    <!-- Loading -->
    <div v-if="!currentVerse">Loading...</div>

    <!-- Verse Display -->
    <div v-else class="psalm-section">
      <h1 class="cross">🕇</h1>
      <h2 class="reference">{{ currentVerse.reference }}</h2>
      <p class="text-en">{{ currentVerse.text_en }}</p>
      <p class="text-ta">{{ currentVerse.text_ta }}</p>
      <button @click="nextVerse" class="next-btn">Next Verse</button>
      <button @click="prevVerse" class="prev-btn">Previous Verse</button>
    </div>

    <!-- Music Player -->
    <div class="music-section">
      <h3>🎵 Psalm Music Player</h3>
      <p class="current-song">Now Playing: {{ currentSongName }}</p>
      <audio ref="audioPlayer" :src="currentMusic" @ended="nextSong"></audio>
      <div class="player-controls">
        <button @click="playAudio">Play</button>
        <button @click="pauseAudio">Pause</button>
        <button @click="nextSong">Next Song</button>
      </div>
    </div>

    <!-- Images -->
    <div class="images-section">
      <h3>🖼 Psalm Images</h3>
      <div class="image-thumbnails">
        <img
          v-for="(img, idx) in images"
          :key="idx"
          :src="img"
          @click="openImage(idx)"
          class="thumbnail"
        />
      </div>
    </div>

    <!-- Image Popup -->
    <div class="image-popup" v-if="showImagePopup" @click="closeImage">
      <img :src="images[currentImageIndex]" class="popup-image" />
      <button class="close-btn" @click.stop="closeImage">✖</button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from "vue";
import versesData from "../data/verse.json"; // merged array

const verses = ref(versesData);
const current = ref(0);
const currentVerse = computed(() => verses.value[current.value] || null);

function nextVerse() {
  current.value = (current.value + 1) % verses.value.length;
}

function prevVerse() {
  if (verses.value?.length) {
    current.value = (current.value - 1 + verses.value.length) % verses.value.length;
  }
}

// ===== Music =====
const musicIndex = ref(0);
const musicFiles = [
  "/audio/Sakhaya_Maname.mp3",
  "/audio/Kana_Oorin_Kalyanathil.mp3",
  "/audio/Kartharai_Thedina_Natkal.mp3",
  "/audio/Raja_Ummai.mp3",
  "/audio/Irrakkangalin.mp3",
  "/audio/Magalae_Seeyon.mp3",
  "/audio/Elutnthu_Bethel.mp3",
];

const currentMusic = computed(() => musicFiles[musicIndex.value]);
const currentSongName = computed(() => currentMusic.value.split("/").pop());
const audioPlayer = ref(null);

function playAudio() { audioPlayer.value?.play(); }
function pauseAudio() { audioPlayer.value?.pause(); }
function nextSong() { musicIndex.value = (musicIndex.value + 1) % musicFiles.length; }

watch(musicIndex, () => {
  audioPlayer.value?.load();
  audioPlayer.value?.play();
});

// ===== Images =====
const images = [
  "/images/img1.jpg",
  "/images/img2.jpg",
  "/images/img3.jpg"
];
const showImagePopup = ref(false);
const currentImageIndex = ref(0);

function openImage(i) { currentImageIndex.value = i; showImagePopup.value = true; }
function closeImage() { showImagePopup.value = false; }
</script>

<style>
/* General layout */
.psalms-content { 
  max-width: 600px; 
  margin: auto; 
  padding: 10px; 
  display: flex; 
  flex-direction: column; 
  gap: 20px; 
  position: relative; 
  overflow: hidden; 
}

/* Bold + cursive verses */
.text-en, .text-ta {
  font-weight: 600;
  font-style: italic;
  font-family: 'Dancing Script', cursive;
  text-align: center;
  margin: 5px 0;
  font-size: 1.1em;
}

/* English verse */
.text-en {
  font-weight: 600;
  font-style: italic;
  font-family: 'Dancing Script', cursive;
  text-align: center;
  margin: 5px 0;
  font-size: 1.1em;
}

/* Tamil verse */
.text-ta {
  font-weight: 700; /* stronger bold for Tamil */
  font-style: italic;
  font-family: 'Noto Sans Tamil', 'Latha', sans-serif; /* Use Tamil-supporting font */
  text-align: center;
  margin: 5px 0;
  font-size: 1.1em;
}

.image-popup {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.9); /* darker overlay */
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  padding: 20px; /* some spacing from screen edges */
}

.popup-image {
  width: auto;
  height: 80vh; /* scale to 80% of viewport height */
  max-width: 95vw; /* scale to 95% of viewport width */
  object-fit: contain;
  border-radius: 10px;
  box-shadow: 0 0 20px rgba(0,0,0,0.5);
}

/* Full page rainbow background */
body, html, #app {
  height: 100%;
  padding: 0;
  max-width: 600px;
  margin: auto;
  display: flex;
  flex-direction: column;
  gap: 20px;

  /* Rainbow gradient background */
  background: linear-gradient(270deg, #ff0000, #ff7f00, #ffff00, #00ff00, #0000ff, #4b0082, #8b00ff);
  background-size: 1400% 1400%;
  animation: rainbowBG 20s ease infinite;

  font-family: sans-serif;
  color: white; /* Makes text readable */
}

/* Keyframes for rainbow animation */
@keyframes rainbowBG {
  0% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
  100% { background-position: 0% 50%; }
}

/* Optional: Remove container styles */
.psalms-content {
  max-width: none;
  margin: 0;
  padding: 20px; /* optional for spacing inside page */
  display: flex;
  flex-direction: column;
  gap: 20px;
}

/* Existing styles */
.cross { font-size: 3em; text-align: center; }
.reference { font-weight: bold; text-align: center; margin: 10px 0; }
.player-controls { display: flex; justify-content: center; gap: 10px; margin-top: 10px; }
.images-section { text-align: center; }
.image-thumbnails { display: flex; gap: 10px; justify-content: center; flex-wrap: wrap; }
.thumbnail { width: 80px; height: 80px; object-fit: cover; border-radius: 6px; cursor: pointer; border: 1px solid #ccc; }
.image-popup { position: fixed; inset: 0; background: rgba(0,0,0,0.8); display: flex; justify-content: center; align-items: center; z-index: 1000; }
.popup-image { max-width: 90%; max-height: 90%; border-radius: 10px; }
.close-btn { position: absolute; top: 15px; right: 20px; font-size: 1.8em; color: white; background: transparent; border: none; cursor: pointer; }
</style>

<!-- Google Fonts for cursive -->
<link href="https://fonts.googleapis.com/css2?family=Dancing+Script&display=swap" rel="stylesheet">
<link href="https://fonts.googleapis.com/css2?family=Dancing+Script&family=Noto+Sans+Tamil&display=swap" rel="stylesheet">
