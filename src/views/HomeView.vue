<script setup>
import ImageLoader from '@/modules/ImageLoader.vue'
import { ref } from 'vue'
import AppButton from '@/components/AppButton.vue'
import ImageLoaderHeavy from '@/modules/ImageLoaderHeavy.vue'
const isEnabledCache = ref(false)
const toggleLoader = () => {
  isEnabledCache.value = !isEnabledCache.value
}
</script>

<template>
  <div class="app-wrapper">
    <div class="button-wrapper">
      <AppButton :class="{ active: !isEnabledCache }" @click="toggleLoader">New</AppButton>
      <AppButton :class="{ active: isEnabledCache }" @click="toggleLoader">Old</AppButton>
    </div>
    <div class="images-wrapper">
      <ImageLoader v-if="isEnabledCache" />
      <ImageLoaderHeavy v-else />
    </div>
  </div>
</template>

<style scoped lang="scss">
.app-wrapper {
  display: flex;
  flex-direction: column;
  gap: 30px;
}
.button-wrapper {
  display: flex;
  gap: 10px;
  .custom-button {
    filter: grayscale(1);
    &.active {
      filter: none;
    }
  }
}
.images-wrapper {
  position: relative;
  justify-content: center;
  align-items: center;
}
</style>
