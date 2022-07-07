<template>
    <div class="w-full">
        <!-- list album -->
        <div class="grid grid-cols-2 gap-8 w-full" v-if="listAlbum.length !== 0 && showAlbum">
            <div v-for="img in listAlbum" :key="img.id" class="flex space-x-2 border-1 shadow-lg rounded-md bg-white p-4 w-full cursor-pointer">
                <img src="~/assets/flower.jpg" alt="" class="cursor-pointer" width="200px" height="200px" v-on:click="showListPhoto(img.albumId)">
                <div class="flex flex-col space-y-4">
                    <p class="text-xl capitalize">
                    Album {{ img.albumId }}
                    </p>
                    <div class="flex space-x-2">
                        <button class="text-center px-4 py-2 rounded-md text-white bg-red-500 text-nowrap" v-on:click="deleteAlbum(img.albumId)">
                            Hapus Album
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <!-- photo list -->
        <div v-if="!showAlbum">
            <!-- filter -->
            <div class="flex justify-between mb-6">
                <Search v-on:search="onSearch"></Search>
                <Filters v-on:filter="onSorting"></Filters>
            </div>
            <!-- add image -->
            <div class="flex justify-end">
                <button
                    class="
                    px-4
                    py-2
                    text-center
                    space-x-1
                    rounded-2xl
                    bg-blue-700
                    text-white
                    mb-4
                    "
                    v-on:click="showAddImage = true"
                    v-if="!showAlbum"
                >
                    + Tambah Gambar
                </button>

            </div>
            <!-- form add -->
            <div class="flex flex-col mx-auto area-outer space-y-4 mb-5 px-4 py-4" v-if="showAddImage">
                <!-- input title image -->
                <div class="area-outer-grid items-center">
                    <p class="text-lg">Title :</p>
                    <input type="text" class="rounded-lg border border-1 border-green-300 px-3 py-2" placeholder="masukkan judul gambar" v-model="formRequest.title">
                </div>
                <!-- input url image -->
                <div class="area-outer-grid items-center">
                    <p class="text-lg">Gambar :</p>
                    <input type="text" class="rounded-lg border border-1 border-green-300 px-3 py-2" placeholder="masukkan url gambar" v-model="formRequest.url">
                </div>
                <!-- input album type -->
                <div class="area-outer-grid items-center">
                    <p class="text-lg">Album :</p>
                    <select v-model="formRequest.albumId" class="rounded-lg border border-1 border-green-300 px-3 py-2">
                        <option value="">Pilih Album</option>
                        <option v-for="item in 100" :key="item" :value="item">
                            Album ~ {{item}}
                        </option>
                    </select>
                </div>

                <button class="px-4 py-2 bg-blue-700 text-white rounded-3xl" v-on:click="addPhoto()">
                    Submit
                </button>
            </div>

            <!-- form change image name -->
            <div class="flex flex-col items-center area-outer rounded-md px-4 py-2" v-if="showChangeTitle">
                <!-- input title image -->
                <div class="grid grid-cols-3 items-center">
                    <p class="text-lg">Title :</p>
                    <input type="text" class="rounded-lg border border-1 border-green-300 px-3 py-2" placeholder="masukkan judul gambar" v-model="formRequest.title">
                    <button class="px-4 py-2 bg-yellow-700 text-white rounded-2xl" v-on:click="changeTitlePhoto()">
                        Edit
                    </button>
                </div>
            </div>

            <!-- form move photo -->
            <div class="flex flex-col mx-auto area-outer space-y-4 mb-5 px-4 py-4" v-if="showFormMoveAlbum">
                <div class="area-outer-grid items-center">
                    <!-- input title image -->
                    <p class="text-lg">Title :</p>
                    <input type="text" class="rounded-lg border border-1 border-green-300 px-3 py-2 capitalize bg-green-400 text-white" placeholder="masukkan judul gambar" v-model="formRequest.title" readonly>
                </div>
                <div class="area-outer-grid">
                    <p class="text-lg">Album :</p>
                    <select v-model="formRequest.albumId" class="rounded-lg border border-1 border-green-300 px-3 py-2">
                        <option value="">Pilih Album</option>
                        <option v-for="item in 100" :key="item" :value="item">
                            Album ~ {{item}}
                        </option>
                    </select>
                </div>

                <button class="px-4 py-2 bg-green-700 text-white rounded-3xl" v-on:click="movePhoto()">
                    Pindahkan
                </button>
            </div>

            <!-- image list -->
            <div class="grid grid-cols-2 gap-8 w-full" v-if="listImage.length !== 0">
                <div v-for="img in listImage" :key="img.id" class="flex space-x-2 border-1 shadow-lg rounded-md bg-white p-4 w-full curs">
                    <img :src="img.url" alt="" width="200px" height="200px">
                    <div class="flex flex-col space-y-4">
                        <p class="text-xl capitalize">
                            {{ img.title }}
                        </p>
                        <div class="flex space-x-2">
                            <button class="text-center px-4 py-2 rounded-md text-white bg-yellow-400 text-nowrap" v-on:click="getDetailPhoto(img)">
                                Ubah Nama Gambar
                            </button>
                            <button class="text-center px-4 py-2 rounded-md text-white bg-red-500 text-nowrap" v-on:click="deletePhoto(img.id, img.albumId)">
                                Hapus Gambar
                            </button>
                        </div>
                        <button class="text-center px-4 py-2 rounded-md text-white bg-green-400 text-nowrap w-full" v-on:click="getDetailPhoto(img, false)">
                            Pindahkan Foto
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script lang="ts">
interface ImageInterface {
    id: string;
    title: string,
    url: string,
    albumId: number
}
import Vue from 'vue';
import axios from 'axios';
// import VueTailwindPagination from '@ocrv/vue-tailwind-pagination'
import { PropType } from 'vue/types/v3-component-props';
export default Vue.extend({
    props: { listAlbum: {
        type: Array as PropType<ImageInterface[]>
    } },
    data() {
        return {
            listImage: [] as ImageInterface[],
            showAlbum: true,
            baseUrl: 'http://localhost:4500/api',
            formRequest: {
                title: '',
                url: '',
                albumId: '',
                id: ''
            },
            showAddImage: false,
            showChangeTitle: false,
            showFormMoveAlbum: false,
            keyword: '',
            sort: ''
        }
    },
    methods: {
        getListImage(albumId: number){
            const url = `${this.baseUrl}/public/album/${albumId}?sort=${this.sort}&skip=0&limit=20&title=${this.keyword}`;
            axios.get(url).then((res) => {
                this.listImage = res.data.photos;
            });
        },

        resetForm(obj: any){
            for (const key in obj) {
                obj[key] = '';
            }
            return obj;
        },

        addPhoto() {
            const body = {
                albumId : +this.formRequest.albumId,
                title: this.formRequest.title,
                url: this.formRequest.url,
                thumbnailUrl: this.formRequest.url
            }
            const url = `${this.baseUrl}/admin/photo`;
            axios.post(url, body).then(res => {
                this.showAddImage = false;
                this.getListImage(body.albumId);
                this.resetForm(this.formRequest);
                alert('Photo berhasil ditambahkan');
            })
        },

        showListPhoto(albumId: number){
            this.showAlbum = false;
            this.formRequest.albumId = albumId.toString();
            this.getListImage(albumId);
        },

        deleteAlbum(albumId: number){
            const url = `${this.baseUrl}/admin/album/${albumId}`;
            axios.delete(url).then(res => {
                alert(`Album telah dihapus`)
                this.$emit('updateList', true);
            })
        },

        deletePhoto(photoId: string, albumId: number){
            const url = `${this.baseUrl}/admin/photo/${photoId}`;
            axios.delete(url).then(res => {
                alert(`Photo telah dihapus`)
                this.getListImage(albumId);
            })
        },
        getDetailPhoto({id, title, albumId}: ImageInterface, isChangeTitle = true){
            isChangeTitle ? this.showChangeTitle = true : this.showFormMoveAlbum = true;
            this.formRequest.title = title;
            this.formRequest.id = id;
            this.formRequest.albumId = albumId.toString();
        },

        changeTitlePhoto(){
            const url = `${this.baseUrl}/admin/photo/name/${this.formRequest.id}`;
            const body = {
                title: this.formRequest.title
            }
            axios.put(url, body).then(res => {
                this.getListImage(+this.formRequest.albumId);
                alert('Nama photo berhasil diganti');
                this.showChangeTitle = false;
                this.resetForm(this.formRequest);
            })
        },
        getDetailAlbum({albumId}: ImageInterface){
            this.showChangeTitle = true;
            this.$emit('albumId', albumId);
            this.$emit('changeAlbumName', true)
        },
        movePhoto(){
            const url = `${this.baseUrl}/admin/photo/album/${this.formRequest.id}`;
            const body = {
                album: this.formRequest.albumId
            }
            axios.put(url, body).then(res => {
                this.getListImage(+this.formRequest.albumId);
                alert(`Foto berhasil dipindahkan ke album ${body.album}`);
                this.showFormMoveAlbum = false;
            })
        },
        onSearch(keyword: string) {
            this.keyword = keyword;
            this.getListImage(+this.formRequest.albumId);
        },
        onSorting(sort: string){
            this.sort = sort;
            this.getListImage(+this.formRequest.albumId)
        }
    },

})
</script>

<style lang="css">
.text-nowrap {
    white-space: nowrap;
}

.area-outer {
    border: 3px dashed #f4f4f4;
    width: 40%;
}

.area-outer-grid {
    display: grid;
    grid-template-columns: 15% auto;
    gap: 10px;
}

:disabled {
    background: #c4c4c4;
    cursor: not-allowed;
}
</style>