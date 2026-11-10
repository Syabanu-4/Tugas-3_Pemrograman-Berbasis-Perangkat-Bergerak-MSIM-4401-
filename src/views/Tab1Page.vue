<template>
  <ion-page>
    <ion-content class="ion-padding content-bg">
      
      <!-- Tombol Refresh di Bagian Atas -->
      <div class="refresh-container">
        <ion-button expand="block" class="btn-refresh" @click="fetchCryptoData">
          Refresh
        </ion-button>
      </div>

      <!-- Indikator Loading -->
      <div v-if="loading" class="loading-container">
        <ion-spinner name="crescent" color="primary"></ion-spinner>
        <p>Memuat data kripto...</p>
      </div>

      <!-- Daftar Kripto dalam Bentuk Kartu Kotak-kotak (Grid 3 Kolom) -->
      <div v-else class="crypto-list-container">
        <div v-for="coin in cryptoList" :key="coin.id" class="crypto-row-card">
          <!-- Kolom 1: Rank -->
          <div class="col-rank">
            <span class="label-small">Rank</span>
            <span class="value-large">{{ coin.market_cap_rank }}</span>
          </div>

          <!-- Kolom 2: Nama Koin & Simbol -->
          <div class="col-name">
            <span class="label-small">{{ coin.name }}</span>
            <span class="value-large uppercase">{{ coin.symbol }}</span>
          </div>

          <!-- Kolom 3: USD & Harga -->
          <div class="col-price">
            <span class="label-small">USD</span>
            <span class="value-large">{{ coin.current_price }}</span>
          </div>
        </div>
      </div>

    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue';
import { IonPage, IonContent, IonButton, IonSpinner } from '@ionic/vue';

export default defineComponent({
  name: 'Tab1Page',
  components: {
    IonPage, IonContent, IonButton, IonSpinner
  },
  setup() {
    const cryptoList = ref<any[]>([]);
    const loading = ref(true);

    const fetchCryptoData = async () => {
      loading.value = true;
      try {
        const response = await fetch('https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=7&page=1&sparkline=false');
        const data = await response.json();
        cryptoList.value = data;
      } catch (error) {
        console.error('Gagal mengambil data crypto:', error);
      } finally {
        loading.value = false;
      }
    };

    onMounted(() => {
      fetchCryptoData();
    });

    return {
      cryptoList,
      loading,
      fetchCryptoData
    };
  }
});
</script>

<style scoped>
.content-bg {
  --background: #ffffff;
}

.refresh-container {
  padding: 10px 15px;
  margin-bottom: 10px;
}

.btn-refresh {
  --background: #0066cc;
  --color: #ffffff;
  font-weight: bold;
  font-size: 16px;
  border-radius: 8px;
  text-transform: none;
}

.crypto-list-container {
  padding: 0 15px;
}

.crypto-row-card {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: #fdf5d8; /* Warna krem kuning sesuai modul tugas */
  border: 1px solid #e2d2a2;
  border-radius: 4px;
  padding: 12px 18px;
  margin-bottom: 8px;
}

.col-rank, .col-name, .col-price {
  display: flex;
  flex-direction: column;
}

.col-name {
  flex-grow: 1;
  padding-left: 20px;
}

.col-price {
  text-align: right;
}

.label-small {
  font-size: 12px;
  color: #333333;
}

.value-large {
  font-size: 20px;
  font-weight: bold;
  color: #000000;
}

.uppercase {
  text-transform: uppercase;
}

.loading-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin-top: 50px;
}
</style>
