<template>
  <div class="flex flex-col mx-10 my-10 space-y-6">
    <h1 class="text-3xl font-serif text-center">Halaman Admin</h1>
    <div class="flex justify-between">
      <!-- untuk filter search -->
      <div></div>
    </div>

    <!-- form change album name -->
      <div class="flex flex-col items-center area-outer rounded-md px-4 py-2" v-if="showChangeAlbum">
          <!-- input title image -->
          <div class="flex space-x-2 items-center">
              <p class="text-lg">Album :</p>
              <select v-model="formRequest.albumId" class="rounded-lg border border-1 border-green-300 px-3 py-2">
                  <option value="">Pilih Album</option>
                  <option v-for="item in 100" :key="item" :value="item">
                      Album ~ {{item}}
                  </option>
              </select>
              <button class="px-4 py-2 bg-yellow-700 text-white rounded-xl" v-on:click="changeTitleAlbum()">
                  Edit
              </button>
          </div>
      </div>
    
    <ListAlbum :listAlbum="albumList" @updateList="refreshListAlbum" @albumId="getAlbumId" @changeAlbumName="changeAlbumName"></ListAlbum>

  </div>
</template>


<script lang="ts">
interface ImageInterface {
  id: string;
  title: string,
  url: string,
  albumId: number

}
import Vue from "vue";
import ListAlbum from "~/components/ListAlbum.vue";
import axios from "axios";
export default Vue.extend({
  name: "AdminPage",
  components: {
    ListAlbum
  },
  data() {
    return {
        imageList: [] as ImageInterface[],
        albumList: [] as ImageInterface[],
        formRequest: {
          title: '',
          url: '',
          albumId: ''
        },
        showAddImage: false,
        showAlbum: true,
        idSelected: 1,
        baseUrl: 'http://localhost:4500/api',
        showChangeAlbum: false
    };
  },
    mounted() {
        this.getListAlbum();
    },

  methods: {
    getListAlbum(sort: string = 'ASC') {
        const url = `${this.baseUrl}/public/album?sort=${sort}&skip=0&limit=20`;
        axios.get(url).then((res) => {
            this.albumList = res.data.albums;
        });
    },
    refreshListAlbum(isUpdate: boolean){
      if(isUpdate){
        this.getListAlbum();
      }
    },
    changeTitleAlbum(){
        const url = `${this.baseUrl}/admin/photo/album/${this.idSelected}`;
        const body = {
            album: this.formRequest.albumId
        }
        axios.put(url, body).then(res => {
            // this.getListImage(+this.formRequest.albumId);
            alert('Nama album berhasil diganti');
            this.$emit('updateList', true);
            this.showChangeAlbum = false;
            this.getListAlbum();
        })
    },
    getAlbumId(id: number){
      this.idSelected = id;
      this.formRequest.albumId = id.toString();
      console.log(this.idSelected)
    },
    changeAlbumName(isShow: boolean) {
      this.showChangeAlbum = isShow;
    }
  },
});
</script>

<style>
input:focus {
  border: 0px solid transparent !important;
}
input {
  width: 250px;
}
</style>