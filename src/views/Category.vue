<template>
  <div id="Category">
    <!-- <v-btn>Education</v-btn>
    <v-btn>Fashion</v-btn>
    <v-btn>People</v-btn>
    <v-btn>Science & Technology</v-btn>
    <v-btn>Music</v-btn>

     <v-divider></v-divider> -->
     
     <!-- <v-data-table :headers="headers" :items="categories">
      <template v-slot:item="{ item }">
        <tr>
          <td>{{ item.categoryTitles }}</td>
        </tr>
      </template>
     </v-data-table> -->

    <h2>채널 카테고리</h2>
    <div class="btnBox">
        <v-btn class="chuga" @click="openCategoryModal">추가</v-btn>
    </div>

    <v-tabs class="mt-5" v-model="tab">
      <v-tab>카테고리 리스트</v-tab>
    </v-tabs>
    <v-tabs-items v-model="tab">

      <!-- 새로만든거 -->
      <v-tab-item>
      <v-data-table  :headers="headers" :items="categories">
      <template v-slot:item="{ item }">
        <tr>
          <td>{{ item.title }}</td>
          <td>{{ item.description }}</td>
          <td>{{ item.createdAt | moment('YYYY-MM-DD HH:mm:ss') }}</td>
          <td>{{ item.updatedAt  | moment('YYYY-MM-DD HH:mm:ss') }}</td>
          <td>
            <v-btn icon @click="deleteCate(item)">
              <v-icon>mdi-delete-forever</v-icon>
            </v-btn>
            <v-btn icon @click="editCate(item)">
              <v-icon>mdi-pencil</v-icon>
            </v-btn>
          </td>
        </tr>
      </template>
      </v-data-table>
      </v-tab-item>

      <CategoryModal
      :openDialog="statusModal"
      v-on:closeDialog="closeCategoryModal">
      </CategoryModal>
    </v-tabs-items>
    
  </div>
</template>
<script>

import moment from 'moment';
import axios from 'axios';
// import VideoModal from '@/components/Modal/VideoModal.vue';
import CategoryModal from '@/components/Modal/CategoryModal.vue';
// import { response } from 'express';

export default {
  name: 'Category',
  //mixins: [VideoModal],
  data: () => ({

    statusModal: false,

    headers: [
      { text: '제목', align: 'start', value: 'title' },
      { text: '설명', value: 'description' },
      { text: '생성날짜', value: 'createdAt' },
      { text: '수정날짜', value: 'updatedAt' },
      { text: 'ACT', value: 'ACT'},
    ],
    //videos: [],

    categories: [], // 카테고리 데이터
    // categoryTitles: [], // 카테고리 타이틀만

    tab: null,
    loading: false,
  }),
  components: {
    CategoryModal,
  },
  watch: {
    $router(to, from){
      if(to.path !== from.path){
        this.getCategories();
      }
    }
  },

methods:{
    openCategoryModal() {
      this.statusModal = true;
      console.log('-- open : ', this.statusModal);
    },
    closeCategoryModal() {
      this.statusModal = false;
      console.log('-- close : ', this.statusModal);
    },

        setDataFormat(value) {
            const result = moment(value).format('YYY-MM-DD HH:mm:ss');
            return result;
        },
        setCalDate(value) {
            const result = moment(value).fromNow();
            return result;            
        },


        async getCategories() {
          this.categoryLoading = true;
          await axios
            .get(process.env.VUE_APP_API + '/categories', {
              headers: {
                Authorization: `Bearer ${localStorage.getItem('token')}`,
              },
            })
            .then((response) => {
              console.log('getCategories - response : ', response);
              this.categories = response.data.data;
              this.categoryTitles = response.data.data.map(
                (category) => category.title
              );
            })
            .catch((error) => {
              console.log('getCategories - error : ', error);
            })
            .finally(() => {
              this.categoryLoading = false;
            });
        },


        // async getVideos(){
        // await axios
        // .get(process.env.VUE_APP_API+'/categories',{
        //     headers:{
        //         Authorization: `Bearer ${localStorage.getItem('token')}`
        //     },
        // })
        // .then((response)=>{
        //   this.videos = response.data.data;
        //   console.log('getVideos - response', response,response.data.data)

        //   })
        
        // .catch((error)=>{console.log('getVideos - error',error)})

        // .finally(()=>{
        //   this.loading = false;
        // });
        // },


        editCate(category) {
          //console.log(category);
          console.log(category._id)
          this.$router.push({
            name: 'Edit',
            query: { data: category._id },
          });
        },


        async deleteCate(category) {
          console.log('category');
          if(confirm('삭제하시겠습니까?')) {
            await axios
            .delete(process.env.VUE_APP_API + '/categories/' + category._id
            //+ category.id
            , {
              headers: {
                Authorization: `Bearer ${localStorage.getItem('token')}`,
              },
            })
            .then((response) => {
              console.log('deleteCate - response : ', response);
              alert('삭제되었습니다.');
              this.getCategories();
            })
            .catch((error) => {
              console.log('deleteCate - error : ', error);
            });
          } else return;
        },
    },

  mounted() {
    this.getCategories();
  },


};
</script>
<style>
#Category {
  margin: 30px;
}

.teahead {
  width: 100%;
  border: 1px solid #fff;
  border-collapse: collapse;
  margin: 10px auto;
  table-layout: fixed;
}
.teahead th, .teahead td {
  border: 1px solid #fff;
}
.teahead th {
  background-color: rgb(130, 161, 180);
  color: #fff;
  font-size: 18;
  padding: 5px 0px;
}
.teahead td {
  font-size: 15px;
  padding: 7px;
}
.chuga {
  color: #fff !important;
  font-weight: bold;
  background-color: rgb(180, 180, 180) !important;
}
</style>
