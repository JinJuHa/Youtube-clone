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
              <v-btn class="bB" @click="show = !show">댓글{{ comment.replies.length }}
                </v-btn>

               <v-btn icon class="delB" @click="deleteComment">
                 <v-icon>mdi-delete-forever</v-icon>
               </v-btn>
            </div>
      </div>
    </v-row>

    <v-expand-transition>
        <div v-show="show">
          <div class="AddReplyAdd">
            <form v-on:submit.prevent="submitReplyForm">
                <v-text-field id="AddReplycom" type="AddReplycom"  placeholder="댓글 추가하기" v-model="Replytext"></v-text-field>
          <div class="AddReplyB">
             <v-btn type="submit">입력</v-btn>
          </div>
            </form>
          </div>
                <!-- 대댓글 리스트 -->
          <div class="AddCommentList">
            <!-- <AddCommentList
            v-for="reply in replies"
            :key="reply.id"
            :reply="reply">
          </AddCommentList> -->

            <!-- v-for="replies in comment"
          :key=" replies._id"
          :replies="replies" -->
            <v-card flat tile class="AddCommentList"
              v-for="reply in  comment.replies"
              :key="reply._id"
            >
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
                  <div class="commentchuga" >
                        <span class="comName">{{ reply.userId.channelName }}</span>
                        <span class="comTime">{{ setCalDate(reply.createdAt) }}</span>
                        <p class="comText" >{{ reply.text }}</p>
                        <!-- <v-btn @click="deleteComment">삭제</v-btn> -->
                  </div>
                          
                              </v-row>
                </div>
              </v-card>
          </div>
        </div>
    </v-expand-transition>

    </div>
  </v-card>
</template>
<script>
import SetFormat from '@/mixins/SetFormat.vue';
import axios from 'axios';
//import AddCommentList from '@/components/AddCommentList.vue'

export default {
  name: 'CommentList',
  mixins: [SetFormat],
  data: () => ({
    show: false,
    Replytext: '',
    replies: [],
  }),
    props: {
    comment: {
      type: Object,
      required: true,
    },
    // replies: {
    //   type: Object,
    //   required: true,
    // },
  },

  computed: {
  },
  components: {
    //AddCommentList,
  },

  methods: {
    deleteComment(){
      this.$emit('deleteComment');
    },
    // submitReplyForm(){
    //   this.$emit('submitReplyForm', this.Replytext, this.comment);
    // },
    // data(){
    //   return {
    //     Replytext:'',
    //   };
    // },
          async submitReplyForm() {
          //console.log('코멘트 아이디', this.comment)
          await axios
            .post(process.env.VUE_APP_API + '/replies', {
              'text' : this.Replytext,
              'commentId' : this.comment.id,
              'userId': JSON.parse(localStorage.getItem('user')).id,
            },
            {
              headers: {
                Authorization: `Bearer ${localStorage.getItem('token')}`,
              },
            })
            .then((response) => {
              console.log('AddReplychuga - response : ', response);
              // this.getWatchData(this.$route.params.id);
              this.Replytext = null;
              //this.getReply();
            })
            .catch((error) => {
              console.log('AddReplychuga - error : ', error);
            });
        },

//         async getReply() {
//           //console.log(this.commentId)
//           //console.log('코멘트 아이디',this.comment.id)
// //       const videoId = this.watchVideo.id;
// // if (videoId) {
//       await axios
//         .get(process.env.VUE_APP_API + '/replies', {
//           headers: {
//             Authorization: `Bearer ${localStorage.getItem('token')}`,
//           },
//         })
//         .then((response) => {
//           console.log('getReply - response : ', response, response.data.data);
//           this.replies = response.data.data;
//         })
//         .catch((error) => {
//           console.log('getReply - error : ', error);
//         });
// //}
//     },
  },
  // mounted() {
  //   this.getReply();
  // }
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
  background-color: rgb(250, 250, 250) !important;
}

.BBB {
  width: 50vw;
  display: flex;
  justify-content: space-between;
}
/* .bB {
  width: 64px;
  box-shadow: none;
} */
.commentchuga {
  background-color: rgb(250, 250, 250) !important;
}

.AddCommentList {
  margin-top: 20px;
  background-color: rgb(250, 250, 250) !important;
}

</style>