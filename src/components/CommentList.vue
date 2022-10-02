<template>
  <v-card flat tile class="CommentList" router :to="`/watch/comments`">
    <div class="commentList">
                  <v-row
              class="spacer"
              no-gutters
            >
              <v-col
                cols="4"
                sm="2"
                md="1"
              >
     <div class="channelProfile">
        <v-avatar size="40px" color="red" class="white--text">
          <h3>{{ comment.userId.channelName.split('')[0].toUpperCase() }}</h3>
        </v-avatar>
      </div>
      </v-col>
      <div class="commentchuga">
            <span class="comName">{{ comment.userId.channelName }}</span>
            <span class="comTime">{{ setCalDate(comment.createdAt) }}</span>
            <p class="comText">{{ comment.text }}</p>
            <v-btn @click="deleteComent(comment)">삭제</v-btn>
      </div>
              
                  </v-row>
    </div>
  </v-card>
</template>
<script>
import SetFormat from '@/mixins/SetFormat.vue';
import axios from 'axios';

export default {
  name: 'CommentList',
  mixins: [SetFormat],
  data: () => ({
    comments: [],
  }),
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
              //this.comments = response.data.data;
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

.comName {
  font-weight: bold;
}

.comTime {
  margin-left: 5px;
  font-size: 12px;
  color: rgb(116, 116, 116);
}

.comText {
  margin-top: 5px;
  margin-bottom: 5px;
}
</style>