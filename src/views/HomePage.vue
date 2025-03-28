<script setup lang="ts">
import { onMounted, ref } from 'vue';
import axios from 'axios';
import { Swiper, SwiperSlide } from 'swiper/vue';
import 'swiper/css';
import 'swiper/css/scrollbar';
import '@ionic/vue/css/ionic-swiper.css';

interface Riddle {
  riddle: string;
  answer: string;
}

const riddles = ref<Riddle[]>([]);
const isLoading = ref<boolean>(false);

const loadMoreRiddles = async (event: any) => {
  isLoading.value = true;
  try {
    const response = await axios.get('https://riddles-api.vercel.app/random');
    if (response.status === 200) {
      riddles.value.push(response.data);
    }
  } catch (error) {
    console.error('Failed to load riddle:', error);
  } finally {
    isLoading.value = false;
    event.target.complete();
  }
};

const refreshRiddles = async (event: any) => {
  try {
    const response = await axios.get('https://riddles-api.vercel.app/random');
    if (response.status === 200) {
      riddles.value.unshift(response.data);
    }
  } catch (error) {
    console.error('Failed to refresh riddle:', error);
  } finally {
    event.target.complete();
  }
};

onMounted(() => {
  loadMoreRiddles({ target: { complete: () => {} } });
});
</script>

<template>
  <ion-page>
    <ion-header>
      <ion-toolbar color="primary">
        <ion-title>Riddles</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content>
      <ion-refresher slot="fixed" @ionRefresh="refreshRiddles">
        <ion-refresher-content pulling-text="Pull to refresh" refreshing-spinner="bubbles"></ion-refresher-content>
      </ion-refresher>
      <Swiper direction="vertical" class="mySwiper" scrollbar>
        <SwiperSlide v-for="(riddle, index) in riddles" :key="index">
          <Swiper direction="horizontal" class="innerSwiper">
            <SwiperSlide>
              <div class="riddle-container">
                <p>{{ riddle.riddle }}</p>
              </div>
            </SwiperSlide>
            <SwiperSlide>
              <div class="riddle-container">
                <p>{{ riddle.answer }}</p>
              </div>
            </SwiperSlide>
          </Swiper>
        </SwiperSlide>
      </Swiper>
    </ion-content>
  </ion-page>
</template>

<style scoped>
:root {
  --ion-color-primary: #88C0D0;
  --ion-color-primary-rgb: 136, 192, 208;
  --ion-color-primary-contrast: #2E3440;
  --ion-color-primary-contrast-rgb: 46, 52, 64;
  --ion-color-primary-shade: #81A1C1;
  --ion-color-primary-tint: #8FBCBB;
  --ion-color-secondary: #81A1C1;
  --ion-color-secondary-rgb: 129, 161, 193;
  --ion-color-secondary-contrast: #2E3440;
  --ion-color-secondary-contrast-rgb: 46, 52, 64;
  --ion-color-secondary-shade: #5E81AC;
  --ion-color-secondary-tint: #88C0D0;
  --ion-color-tertiary: #8FBCBB;
  --ion-color-tertiary-rgb: 143, 188, 187;
  --ion-color-tertiary-contrast: #2E3440;
  --ion-color-tertiary-contrast-rgb: 46, 52, 64;
  --ion-color-tertiary-shade: #81A1C1;
  --ion-color-tertiary-tint: #88C0D0;
  --ion-color-light: #ECEFF4;
  --ion-color-light-rgb: 236, 239, 244;
  --ion-color-light-contrast: #2E3440;
  --ion-color-light-contrast-rgb: 46, 52, 64;
  --ion-color-light-shade: #E5E9F0;
  --ion-color-light-tint: #D8DEE9;
  --ion-color-medium: #4C566A;
  --ion-color-medium-rgb: 76, 86, 106;
  --ion-color-medium-contrast: #ECEFF4;
  --ion-color-medium-contrast-rgb: 236, 239, 244;
  --ion-color-medium-shade: #434C5E;
  --ion-color-medium-tint: #3B4252;
  --ion-color-dark: #2E3440;
  --ion-color-dark-rgb: 46, 52, 64;
  --ion-color-dark-contrast: #ECEFF4;
  --ion-color-dark-contrast-rgb: 236, 239, 244;
  --ion-color-dark-shade: #3B4252;
  --ion-color-dark-tint: #434C5E;
}

.riddle-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 50vh;
  font-size: 24px;
  text-align: center;
  padding: 16px;
  background-color: var(--ion-color-light);
  color: var(--ion-color-dark);
  border: 1px solid var(--ion-color-medium);
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}
</style>
