<script setup>
import AppButton from '@/components/AppButton.vue'
import { ref, computed, onBeforeUnmount } from 'vue'

const images = ref([null, null, null, null])
const iteration = ref(0)
const reloadingImages = ref(false)

const timerDuration = ref(0)
const imagesLoading = ref(false)

//TODO think how to delete reloadingImages

const buttonDisabled = computed(() => {
  return timerDuration.value || imagesLoading.value
})

const loadImages = async (imageLinks) => {
  const newImages = []
  imageLinks.forEach((link) => {
    newImages.push(link)
  })
  imagesLoading.value = true
  const promises = newImages.map(async (image) => {
    return new Promise((resolve, reject) => {
      const img = new Image()
      img.onload = () => {
        console.log('loaded')
        resolve()
      }
      img.onerror = () => reject()
      img.src = image
    })
  })
  await Promise.allSettled(promises)
  console.log('all loaded')
  iteration.value++
  images.value = [...newImages]
  imagesLoading.value = false
}

const getLinks = (indexes) => {
  return indexes.map((el) => `/img/${String(el)}.png`)
}
const handleNextButtonClick = async () => {
  if (buttonDisabled.value) return
  const indexes = generateNumbers(4, 8)
  console.log('id', indexes)
  const links = getLinks(indexes)
  await loadImages(links)
  startTimer()
  reloadingImages.value = false
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
    <template v-if="images.length === 4">
      <transition-group name="fade-item" tag="div">
        <img
          v-for="image in images"
          v-show="image"
          :key="`${iteration}-${image}`"
          :src="image"
          alt="Image"
          class="image"
        />
      </transition-group>
    </template>
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
  }

  @for $i from 1 through 4 {
    img:nth-child(#{$i}) {
      top: ($i - 1) * $offset;
      left: ($i - 1) * $offset;
      z-index: $i + 1;
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
