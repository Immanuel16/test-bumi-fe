<template>
  <div class="mx-auto">
    <h2 class="font-bold text-3xl text-center mt-10 mb-6">Halaman Public</h2>
    <div class="flex flex-row justify-between" v-if="isShowPhoto">
      <Search v-on:keyword="getKeyword"></Search>
      <Filters v-on:filter="getFilter"></Filters>
    </div>

    <div class="grid grid-cols-2 gap-4 px-8" v-if="!isShowPhoto">
      <div v-for="album in albumData" :key="album.id">
        <div
          class="flex flex-col justify-center items-center cursor-pointer space-y-3 mb-6"
          v-on:click="onAccessAlbum(album.albumId)"
        >
          <div class="rounded-2xl bg-blue-400 py-2 px-4 w-label">
            <p class="text-center text-3xl text-white">Photo Album {{ album.albumId }}</p>
          </div>
          <img src="~/assets/flower.jpg" class="rounded-2xl" alt="">
        </div>
      </div>
    </div>

    <div class="grid grid-cols-2 gap-4 px-8" v-if="isShowPhoto">
      <div v-for="photo in photoData" :key="photo.id">
        <div class="flex flex-col justify-center items-center cursor-pointer space-y-3 mb-6">
          <div class="rounded-2xl bg-blue-200 py-2 px-4 w-label">
            <p class="text-center text-3xl text-white capitalize">{{ photo.title }}</p>
          </div>
          <img :src="photo.url" class="rounded-xl" alt="">
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import Search from "~/components/Search.vue";
import Filters from "~/components/Filters.vue";
import axios from "axios";

export default Vue.extend({
  components: { Search, Filters },
  name: "UsersPage",

  data() {
    return {
      photoData: [] as any,
      albumData: [] as any,
      isShowPhoto: false,
      keyword: "",
      accessAlbum: 0,
      filter: "",
      page: 1,
      baseUrl: 'http://localhost:4500/api/public'
    };
  },

  mounted() {
    this.getAlbum();
  },

  methods: {
    getAlbum() {
      axios
        .get(`${this.baseUrl}/album?skip=0&limit=20`)
        .then(({data}: any) => {
          this.albumData = data.albums;
        });
    },

    onAccessAlbum(accessAlbum: number) {
      this.accessAlbum = accessAlbum;
      axios
        .get(
          `${this.baseUrl}/album/${accessAlbum}?sort=${this.filter}&skip=0&limit=20&title=${this.keyword}`
        )
        .then(({data}: any) => {
          this.isShowPhoto = true;
          this.photoData = data.photos;
        });
    },

    getKeyword(val: string) {
      this.keyword = val;
      this.onAccessAlbum(this.accessAlbum);
    },

    getFilter(val: string) {
      console.log("heres", val);
      this.filter = val;
      this.onAccessAlbum(this.accessAlbum);
    },
  },
});
</script>

<style>
.user {
  background-color: #00d8d6;
}

.admin {
  background-color: #ff5e57;
}

.w-label {
  width: 75%;
}
</style>
