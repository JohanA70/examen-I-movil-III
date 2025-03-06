<script setup>
import {
  IonContent,
  IonHeader,
  IonPage,
  IonTitle,
  IonToolbar,
  IonCard,
  IonButton,
  IonItem,
  IonLabel,
  IonSelect,
  IonSelectOption,
  IonCardHeader,
  IonCardTitle,
  IonCardContent,
  IonIcon,
} from '@ionic/vue';
import { ref } from 'vue';
import axios from 'axios';
import { star, gitNetwork, alertCircle } from 'ionicons/icons';

const selectedLanguage = ref('');
const repository = ref(null);
const state = ref('empty');

const languages = ['JavaScript', 'Python', 'Java', 'C++', 'Go', 'Ruby', 'PHP', 'Swift'];

const fetchRepository = async () => {
  if (!selectedLanguage.value) {
    state.value = 'empty';
    return;
  }

  state.value = 'loading';

  try {
    const token = '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';
    const response = await axios.get(
      `https://api.github.com/search/repositories?q=language:${selectedLanguage.value}&sort=stars`,
      {
        headers: {
          Authorization: `token ${token}`,
        },
      }
    );

    const repos = response.data.items;
    if (repos.length > 0) {
      repository.value = repos[Math.floor(Math.random() * repos.length)];
      //console.log('Repository:', repository );
      state.value = 'success';
    } else {
      state.value = 'error';
    }
  } catch (error) {
    //console.error('Error fetching repositories:', error);
    state.value = 'error';
  }
};
</script>

<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>GitHub Repository Finder</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content class="ion-padding">
      <ion-card class="card">
        <ion-item>
          <ion-label>Selecciona un lenguaje</ion-label>
          <ion-select v-model="selectedLanguage" @ionChange="fetchRepository">
            <ion-select-option v-for="lang in languages" :key="lang" :value="lang">
              {{ lang }}
            </ion-select-option>
          </ion-select>
        </ion-item>

        <div v-if="state === 'empty'">
          <ion-card>
            <ion-card-content>Por favor selecciona un lenguaje</ion-card-content>
          </ion-card>
        </div>

        <div v-if="state === 'loading'">
          <ion-card>
            <ion-card-content>Loading, please wait...</ion-card-content>
          </ion-card>
        </div>

        <div v-if="state === 'error'">
          <ion-card color="danger">
            <ion-card-content>Error fetching repositories</ion-card-content>
          </ion-card>
          <ion-button color="danger" expand="full" @click="fetchRepository">
            Click to retry
          </ion-button>
        </div>

        <div v-if="state === 'success'">
          <ion-card>
            <ion-card-header>
              <ion-card-title>{{ repository.name }}</ion-card-title>
            </ion-card-header>
            <ion-card-content>
              <p>{{ repository.description || 'No description available' }}</p>

              <div style="display: flex; align-items: center; gap: 10px; font-size: 14px;">
                <ion-icon :icon="star"></ion-icon>
                <p><strong>{{ repository.stargazers_count }}</strong></p>

                <ion-icon :icon="gitNetwork"></ion-icon>
                <p><strong>{{ repository.forks_count }}</strong></p>

                <ion-icon :icon="alertCircle"></ion-icon>
                <p><strong>{{ repository.open_issues_count }}</strong></p>
              </div>
            </ion-card-content>
          </ion-card>

          <ion-button expand="full" color="dark" @click="fetchRepository">Refresh</ion-button>
        </div>
      </ion-card>
    </ion-content>
  </ion-page>
</template>

<style scoped>
.card {
  max-width: 400px;
  margin: auto;
  padding: 20px;
  border-radius: 12px;
  background-color: white;
  box-shadow: 0px 2px 8px rgba(0, 0, 0, 0.05);
  border: 1px solid #f0f0f0;
  border-radius: 2%;
}


ion-item, ion-card {
  --background: white;
  --color: black;
}

ion-label {
  color: black;
}

ion-select {
  --background: white;
  --color: black;
}

ion-card-content, ion-card-title {
  color: black;
}

ion-button {
  background-color: black;
  color: white;
}
</style>