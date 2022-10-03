<template>
  <v-card flat tile class="CommentList">
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
            <v-img
            v-if="comment.userId.photoUrl !== 'no-photo.jpg'"
            :src="`${process.env.VUE_APP_URL}/uploads/avatars/${channel.photoUrl}`"></v-img>
          <h3>{{ comment.userId.channelName.split('')[0].toUpperCase() }}</h3>
        </v-avatar>
      </div>
      </v-col>
      <div class="commentchuga">
            <span class="comName">{{ comment.userId.channelName }}</span>
            <span class="comTime">{{ setCalDate(comment.createdAt) }}</span>
            <p class="comText">{{ comment.text }}</p>

            <div class="BBB" >
              <v-btn @click="show = !show">댓글{{ comment.replies.length }}</v-btn>

               <v-btn icon class="delB" @click="deleteComment">
                 <v-icon>mdi-delete-forever</v-icon>
               </v-btn>
            </div>
      </div>
    </v-row>

          <v-expand-transition>
        <div v-show="show">
                <v-text-field id="AddReplycom" type="AddReplycom"  placeholder="댓글 추가하기"></v-text-field>
                <!-- 대댓글 리스트 -->
        </div>
        </v-expand-transition>

    </div>
  </v-card>
</template>
<script>
import SetFormat from '@/mixins/SetFormat.vue';

export default {
  name: 'CommentList',
  mixins: [SetFormat],
  data: () => ({
    show: false,
  }),
  props: ['comment'],

  computed: {
  },

  methods: {
    deleteComment(){
      this.$emit('deleteComment');
    },
  },
};
</script>
<style>

.CommentList {
    background-color: rgb(250, 250, 250) !important;
    padding: 15px;
}

/* .commentList {
  border-bottom: 1px solid black;
} */

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
  margin-bottom: 8px;
}

.BBB {
  width: 50vw;
  display: flex;
  justify-content: space-between;
}
</style>