<template>
    <v-dialog  persistent max-width="1000" v-model="dialog">
        <v-card class="modal">

            <div class="modalHeader">
                <h2>댓글 업로드</h2>
              <v-btn icon text @click="closeModal">
                 <v-icon>mdi-close</v-icon>
              </v-btn>
            </div>

            <v-divider></v-divider>

        <ValidationObserver v-slot="{ invalid, validate }">
          <!-- <form class="formStyle" @submit.prevent="handleSubmit(saveCate)"> -->
          <ValidationProvider
            name="댓글"
            rules="required|min:3"
            v-slot="{ errors }">
            <v-text-field
            dense
            filled
            label="댓글 내용"
            :error-messages="errors"
            v-model="text"></v-text-field>
          </ValidationProvider>

              <v-btn
              depressed
              block
              color="blue"
              class="white--text"
              :disabled="invalid || !validate"
              type="submit"
              @click="AddComment"
              >입력</v-btn>

              </ValidationObserver>

        </v-card>
    </v-dialog>
</template>

<script>
import axios from 'axios';
import Validate from '@/mixins/Validate.vue';
// import Watch from '@/views/Watch.vue';

    export default {
        name: 'CommentModal',
        mixins: [Validate],
          props: {
          openDialog: Boolean,
      },
      data: () => ({
             
            text: "",
            videoId: "",
            userId:"",
                 // axiosBody
            commentLoading: false,
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

              async AddComment() {
              this.commentLoading = true;
              console.log('비디오 아이디 찾아',this.watchVideo.id);

              await axios
                .post(process.env.VUE_APP_API + '/comments', {
                  'text' : this.text,
                //   'videoId' : this.watchVideo.id,
                //   'userId': JSON.parse(localStorage.getItem('user')).id,
                },
                {
                  headers: {
                    Authorization: `Bearer ${localStorage.getItem('token')}`,
                  },
                })
                .then((response) => {
                  console.log('getComment - response : ', response);
                  this.comments = response.data.data;
                  // this.categoryTitles = response.data.data.map(
                  //   (category) => category.title
                  // );
                })
                .catch((error) => {
                  console.log('getComment - error : ', error);
                })
                .finally(() => {
                  this.commentLoading = false;
                  this.closeModal();
                });
            },
         },
    }
</script>

<style>
</style>