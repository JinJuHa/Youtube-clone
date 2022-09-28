<template>
    <v-container fluid class="full-heght">
        <h2>카테고리 수정</h2>


<v-container fluid class="white pa-10 mt-5">
    <ValidationObserver v-slot="{ handleSubmit, invalid }">
      <form @submit.prevent="handleSubmit(submitData)">
        <p>카테고리 제목</p>
        <ValidationProvider
          name="카테고리 타이틀"
          rules="required"
          v-slot="{ errors }"
        >
          <v-text-field
            outlined
            type="text"
            :error-messages="errors"
            v-model="title"
          ></v-text-field>
        </ValidationProvider>

        <p>카테고리 내용</p>
        <ValidationProvider
          name="카테고리 내용"
          rules="required"
          v-slot="{ errors }"
        >
          <v-text-field
            outlined
            type="text"
            :error-messages="errors"
            v-model="description"
          ></v-text-field>
        </ValidationProvider>


        <v-btn :disabled="invalid" type="submit">수정</v-btn>
      </form>
    </ValidationObserver>


  </v-container>


    </v-container>
</template>

<script>
import axios from "axios";
import Validate from "@/mixins/Validate.vue";
import Category from "@/views/Category.vue";

    export default {
        name: 'Edit',
        mixins: [Validate, Category],
        data: () => ({
            // 폼데이터
            title: "",
            description: "",
        }),
        methods: {

        cancelEdit() {
            this.$router.go(-1);
        },

        async submitData() {
        const axiosBody = {
        title: this.title,
        description: this.description,
        };
        console.log("submitData - axiosBody", axiosBody);


    
      // const categoryId = this.$route.params.data
      const categoryId = this.$route.query.data
      //console.log('카테고리냐 :', categoryId);

      await axios
        .put(process.env.VUE_APP_API + `/categories/${categoryId}`, axiosBody, {
          headers: {
            Authorization: `Bearer ${localStorage.getItem("token")}`,
          },
        })
        .then((response) => {
          console.log("submitData - response : ", response);
          localStorage.setItem(this.$route.query.data, JSON.stringify(response.data.data));
          this.cancelEdit();
        })
        .catch((error) => {
          console.log("submitData - error : ", error);
        });



        },
        

        mounted() {
          //console.log("edit : ", this.$route.query.data);
          const editCateData = JSON.parse(this.$route.query.data);
          this.formData = editCateData;
          //console.log(this.formData);
        },

        },
    }
</script>

<style>
</style>