<script setup>
import AppButton from '@/components/AppButton.vue'
import { ref, computed, onBeforeUnmount } from 'vue'

const images = ref([null, null, null, null])
const reloadingImages = ref(false)

const timerDuration = ref(0)
const imagesLoading = ref(false)
const counter = ref(0)
//TODO think how to delete reloadingImages

const buttonDisabled = computed(() => {
  return timerDuration.value || imagesLoading.value
})

const setupNewImages = async (imageLinks) => {
  imageLinks.forEach((link) => {
    images.value.push(link)
  })
}

const getLinks = (indexes) => {
  return indexes.map((el) => `/img/${String(el)}.png?nocache=${Date.now()}`)
}
const handleNextButtonClick = async () => {
  if (buttonDisabled.value) return
  imagesLoading.value = true
  const indexes = generateNumbers(4, 8)
  const links = getLinks(indexes)
  setupNewImages(links)
  reloadingImages.value = true
}

const generateNumbers = (num, max) => {
  const uniqueNumbers = []
  const sortedNumbers = []
  for (let i = 0; i < num; i++) {
    const randomIndex = Math.floor(Math.random() * (max - i)) + 1
    let offset = 0
    for (let j = 0; j < i; j++) {
      if (sortedNumbers[j] <= randomIndex + offset) offset++
      else j = i
    }
    sortedNumbers.splice(offset, 0, randomIndex + offset)
    uniqueNumbers.push(randomIndex + offset)
  }
  return uniqueNumbers
}

const increaseCounter = () => {
  counter.value++
  console.log(counter.value)
  if (counter.value === 4) {
    images.value.splice(0, 4)
    imagesLoading.value = false
    startTimer()
    counter.value = 0
  }
}

let timerInterval
const startTimer = () => {
  timerDuration.value = Math.floor(Math.random() * 3000) + 3000
  timerInterval = setInterval(() => {
    timerDuration.value -= 10
    if (timerDuration.value <= 0) {
      timerDuration.value = 0
      clearInterval(timerInterval)
    }
  }, 10)
}

const formattedTimer = computed(() => {
  const seconds = timerDuration.value / 1000
  return seconds.toFixed(2) + 's'
})

onBeforeUnmount(() => {
  clearInterval(timerInterval)
})
</script>

<template>
  <div class="loader-wrapper">
    <img
      v-for="image in images"
      v-show="image"
      :key="image"
      :src="image"
      alt="Image"
      @load="increaseCounter"
      class="image"
    />
    <AppButton v-if="!buttonDisabled" @click="handleNextButtonClick"> Next </AppButton>
    <div v-if="timerDuration" class="timer">{{ formattedTimer }}</div>
  </div>
</template>

<style lang="scss" scoped>
$offset: 20px;

.loader-wrapper {
  width: 200px;
  height: 200px;
  position: relative;

  .custom-button {
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    position: absolute;
    z-index: 10;
  }

  img {
    max-width: 100px;
    max-height: 100px;
    position: absolute;
    transition: opacity 5.3s ease-out;
  }

  @for $i from 1 through 4 {
    img:nth-child(#{$i}) {
      top: ($i - 1) * $offset;
      left: ($i - 1) * $offset;
      z-index: $i + 1;
    }
  }
  @for $i from 5 through 8 {
    img:nth-child(#{$i}) {
      top: ($i - 1) * $offset;
      left: ($i - 1) * $offset;
      opacity: 0;
    }
  }

  .timer {
    position: absolute;
    left: 100%;
    top: 100%;
  }
}

.fade-item-enter-active {
  transition: opacity 5.3s ease-out;
}
.fade-item-enter-from,
.fade-item-leave-to {
  opacity: 0;
}
</style>
