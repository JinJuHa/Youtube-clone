<template>
  <v-card flat tile class="AddCommentList" router :to="`/watch/replies`">
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
            <v-btn @click="deleteComment">삭제</v-btn>
      </div>
              
                  </v-row>
    </div>
  </v-card>
</template>
<script>
import SetFormat from '@/mixins/SetFormat.vue';

export default {
  name: 'CommentList',
  mixins: [SetFormat],
  data: () => ({
    comments: [],
  }),
  props: ['comment'],

  computed: {
  },

  methods: {
  },
};
</script>
<style>
</style>