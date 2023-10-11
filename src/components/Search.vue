<template>
  <v-container class="fill-height">
    <v-responsive class="align-center text-center fill-height">
      <h1 class="text-h2 font-weight-bold">A.M.A</h1>

      <div class="py-14" />

      <v-row class="d-flex align-center justify-center">
        <v-col cols="12" md="7" lg="4">
          <v-text-field
            v-model="text"
            :loading="loading"
            density="compact"
            variant="solo"
            label="Search styling"
            append-inner-icon="mdi-magnify"
            single-line
            hide-details
            @click:append-inner="search"
          ></v-text-field>
        </v-col>
        <v-col cols="12" md="3" lg="2">
          <v-select
            v-model="gender"
            density="compact"
            variant="solo"
            label="Gender"
            single-line
            hide-details
            :items="genders"
          ></v-select>
        </v-col>
      </v-row>

      <v-row class="d-flex">
        <v-col v-for="outfit in outfits" v-bind:key="outfit.id" cols="4" md="3" lg="2">
          <v-img @click="openNewTab(outfit.url)" :src="outfit.images[0]"></v-img>
        </v-col>
      </v-row>
    </v-responsive>
  </v-container>
</template>

<script lang="ts" setup>
import {onMounted, reactive, ref, watch} from "vue";
import axios from "axios";
interface Outfit {
  id: string,
  images: Array<string>,
  gender: string,
  description?: string|undefined,
  postedAt: string,
  url: string,
}

const loading = ref<boolean>(false);
const genders = ref<string[]>(['ALL', 'WOMEN', 'MEN']);
const text = ref<string|null>(null);
const gender = ref<string|null>(null);
const outfits = reactive<Array<Outfit>>([]);
const search = () => {
  alert('on click');
};
const openNewTab = (url: string) => {
  window.open(url, '_blank');
};
const fetchOutfitList = (start: number = 0, size: number = 50, gender: string|null = null) => {
  outfits.splice(0);

  axios
    .get('/api/outfits', {
      params: {
        start: start,
        size: size,
        gender: gender
      }
    })
    .then(function (response) {
      response.data.items.forEach(function(item: any) {
        let outfit: Outfit = {
          id: item.id,
          images: item.images,
          gender: item.gender,
          description: item.description,
          postedAt: item.postedAt,
          url: item.url,
        };
        outfits.push(outfit)
      });
    })
    .catch(function (error) {
      console.log(error);
    })
};

onMounted(fetchOutfitList);
watch(gender, (newGender) => fetchOutfitList(0, 50, newGender));
</script>
