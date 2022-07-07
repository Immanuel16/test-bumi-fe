<template>
  <div>
    <!-- <p class="text-3xl font-bold uppercase text-center mt-10">selamat datang</p> -->
    <div class="flex flex-col justify-center items-center h-screen space-y-24">
      <button class="text-center text-3xl w-button py-4 rounded-md text-white bg-green-300">
        <NuxtLink to="/public">Halaman Public</NuxtLink>
      </button>
        <button class="text-center text-3xl  py-4 rounded-md text-white bg-red-300 w-button">
          <NuxtLink to="/admin">Halaman Admin</NuxtLink>
        </button>
    </div>

  </div>
</template>

<script lang="ts">
import Vue from 'vue';
import ListImage from '~/components/ListImage.vue';

export default Vue.extend({
  name: 'IndexPage',
  data(){
    return{
      listImage: [
      ]
    }
  },
  components: {ListImage}
})
</script>

<style>
.w-button {
  width: 75%;
  height: 10%;
}
</style>
