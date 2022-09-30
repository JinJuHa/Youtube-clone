<template>
  <v-card flat tile class="CommentList" router :to="`/watch/comments`">
    <div class="commentList">
     <div class="channelProfile">
        <v-avatar color="red" class="white--text">
          <h3>{{ comment.userId.channelName.split('')[0].toUpperCase() }}</h3>
        </v-avatar>
      </div>
      <div class="commentchuga">
            <span>{{ comment.userId.channelName }}</span>
            <span>{{ setCalDate(comment.createdAt) }}</span>
            <span>{{ comment.text }}</span>
            <v-btn @click="deleteComent(comment)">삭제</v-btn>
      </div>
    </div>
  </v-card>
</template>
<script>
import SetFormat from '@/mixins/SetFormat.vue';
import axios from 'axios';

export default {
  name: 'CommentList',
  mixins: [SetFormat],
  props: {
    comment: {
      type: Object,
      required: true,
    },
  },

  computed: {
  },

  methods: {
            
            
            async deleteComent(comment) {
          if(confirm('삭제하시겠습니까?')) {
            await axios
            .delete(process.env.VUE_APP_API + '/comments/' + comment.id
            , {
              headers: {
                Authorization: `Bearer ${localStorage.getItem('token')}`,
              },
            })
            .then((response) => {
              console.log('deleteComent - response : ', response);
              alert('삭제되었습니다.');
            })
            .catch((error) => {
              console.log('deleteComent - error : ', error);
            });
          } else return;
        },
  }
};
</script>
<style>

.CommentList {
    background-color: rgb(250, 250, 250) !important;
    padding: 15px;
}
</style>