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
      <ion-toolbar>
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
.riddle-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  font-size: 24px;
  text-align: center;
  padding: 16px;
}
</style>
