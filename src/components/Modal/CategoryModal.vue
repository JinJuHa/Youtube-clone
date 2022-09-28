<template>
    <v-dialog  persistent max-width="1000" v-model="dialog">
        <v-card class="modal">

            <div class="modalHeader">
                <h2>카테고리 업로드</h2>
              <v-btn icon text @click="closeModal">
                 <v-icon>mdi-close</v-icon>
              </v-btn>
            </div>

            <v-divider></v-divider>

        <ValidationObserver v-slot="{ invalid, validate }">
          <!-- <form class="formStyle" @submit.prevent="handleSubmit(saveCate)"> -->
          <ValidationProvider
            name="카테고리"
            rules="required|min:3"
            v-slot="{ errors }">
            <v-text-field
            dense
            filled
            label="카테고리 타이틀"
            :error-messages="errors"
            v-model="title"></v-text-field>
          </ValidationProvider>

            <ValidationProvider
            name="카테고리 내용"
            rules="required|min:3"
            v-slot="{ errors }">
            <v-text-field
            dense
            filled
            label="카테고리 내용"
            :error-messages="errors"
            v-model="description"></v-text-field>
          </ValidationProvider>

              <v-btn
              depressed
              block
              color="blue"
              class="white--text"
              :disabled="invalid || !validate"
              type="submit"
              @click="AddCategories"
              >입력</v-btn>

              <!-- </form> -->
              </ValidationObserver>

        </v-card>
    </v-dialog>
</template>

<script>
import axios from 'axios';
import Validate from '@/mixins/Validate.vue';

    export default {
        name: 'CategoryModal',
        mixins: [Validate],
          props: {
          openDialog: Boolean,
      },
      data: () => ({
             //categories: [], // 카테고리 데이터
             //categoryTitles: [], // 카테고리 타이틀만

              
            title: '',
            description: '',
                 // axiosBody
            categoryLoading: false,
              //saveLoading: false,
      }),

        computed: {
            dialog(props) {
            console.log(props.openDialog);
            return props.openDialog;
            },
        },

        methods: {
              closeModal() {
                this.$emit('closeDialog');
              },

              async AddCategories() {
              this.categoryLoading = true;
              //console.log(this.title , this.description);
              await axios
                .post(process.env.VUE_APP_API + '/categories', {
                  'title' : this.title,
                  'description' : this.description
                },
                {
                  headers: {
                    Authorization: `Bearer ${localStorage.getItem('token')}`,
                  },
                })
                .then((response) => {
                  console.log('getCategories - response : ', response);
                  this.category = response.data.data;
                  // this.categoryTitles = response.data.data.map(
                  //   (category) => category.title
                  // );
                })
                .catch((error) => {
                  console.log('getCategories - error : ', error);
                })
                .finally(() => {
                  this.categoryLoading = false;
                  this.closeModal();
                });
            },

        //     async saveCategory() {
        //       this.saveLoading = true;

        //     const axiosBody = {
        //     title: this.formData.title,
        //     description: this.formData.description,
        //     };
        //     console.log('saveCategory - axiosBody : ', axiosBody);

        //     const CateFormData = new FormData();
        //     CateFormData.append('title', this.formData.title);
        //     CateFormData.append('description', this.formData.description);
        //     for (let item of CateFormData.entries()) {
        //       console.log(item);
        //     }

        // await axios
        // .put(this.url, CateFormData, {
        //   headers: {
        //     Authorization: `Bearer ${localStorage.getItem('token')}`,
        //   },
        // })
        // .then((response) => {
        //   console.log('saveCate - response : ', response);
        // })
        // .catch((error) => {
        //   console.log('saveCate - error : ', error);
        // })
        // .finally(() => {
        //   this.closeModal();
        // });
        //     }

            
         },
        // mounted() {
        //   this.AddCategories();
        // }
    }
</script>

<style>
</style>