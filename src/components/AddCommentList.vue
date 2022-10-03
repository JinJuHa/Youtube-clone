<template>
  <v-card flat tile class="AddCommentList">
    <div class="AddList">
                  <v-row
              class="spacer"
              no-gutters
            >
              <v-col
                cols="4"
                sm="2"
                md="1"
              >
     <div class="AddChannelProfile">
        <v-avatar size="27px" color="red" class="white--text">
          <h5>{{ reply.userId.channelName.split('')[0].toUpperCase() }}</h5>
        </v-avatar>
      </div>
      </v-col>
      <div class="commentchuga">
            <span class="comName">{{ reply.userId.channelName }}</span>
            <span class="comTime">{{ setCalDate(reply.createdAt) }}</span>
            <p class="comText">{{ reply.text }}</p>
            <!-- <v-btn @click="deleteComment">삭제</v-btn> -->
      </div>
              
                  </v-row>
    </div>
  </v-card>
</template>
<script>
import SetFormat from '@/mixins/SetFormat.vue';

export default {
  name: 'AddCommentList',
  mixins: [SetFormat],
  data: () => ({
    replies: [],
  }),
  props: ['reply'],

  computed: {
  },

  methods: {
  },
};
</script>
<style>
.AddCommentList {
    background-color: rgb(250, 250, 250) !important;
    padding: 10px;
}
</style>