<template>
  <v-container id="Watch" class="fill-height" fluid>
    <div class="watchSection fill-height">
      <div class="watchContainer">
        <v-skeleton-loader large tile :loading="videoLoading">
          <v-responsive>
            <video controls style="height: 100%; width: 100%">
              <source :src="videoUrl !== null && videoUrl" type="video/mp4" />
            </video>
          </v-responsive>
        </v-skeleton-loader>

        <div class="videoInfo">
          <h2 class="videoTitle">{{ watchVideo.title }}</h2>

            <span class="Feelings">
              <v-btn class="feelingGood" @click="AddFeeling('like')">
                <v-icon>mdi-hand-pointing-up</v-icon>
                {{ watchVideo.likes }}
              </v-btn>
              <v-btn class="feelingBad" @click="BadFeeling('dislike')">
                <v-icon>mdi-hand-pointing-down</v-icon>
                {{ watchVideo.dislikes }}
              </v-btn>
          </span>
            <span>조회수 {{ watchVideo.views }}회</span>
            <span>&#183;</span>
            <span>{{ setCalDate(watchVideo.createdAt) }}</span>
            <span class="descrip">{{ watchVideo.description }}</span>
            <!-- <div class="channelProfile">
            <v-avatar color="red" class="white--text">
              <h3>{{ watchVideo.userId.channelName.split('')[0].toUpperCase() }}</h3>
            </v-avatar>
          </div> -->
        </div>

        <v-divider></v-divider>

        <div class="comment">
              <p>댓글 {{ watchVideo.comments }} 개</p>
      <div class="commentadd">
        <form v-on:submit.prevent="submitForm"> 
          <div>
            <!-- <label class="commenTitle">댓글</label> -->
            <!-- <input class="commentAdd" type="commentAdd" v-model="text"> -->
              <v-text-field id="commentAdd" type="commentAdd"  placeholder="댓글 추가하기" v-model="text">
              </v-text-field>
          </div>
          <div class="commentAddB">
             <v-btn type="submit">입력</v-btn>
          </div>
        </form>
    </div>


<!-- 여기서부터 댓글 리스트임 -->
      <div class="commentlist">
        <CommentList
          v-for="comment in comments"
          :key="comment.id"
          :comment="comment"
          @deleteComment=deleteComent(comment)
          ></CommentList>
      </div>

          
        </div>
      </div>
    </div>

    <div class="listSection fill-height">
      <div class="listContainer">
        <VideoListCard
          v-for="video in videos"
          :key="video.id"
          :video="video"
          :channel="video.userId"></VideoListCard>
      </div>
    </div>
  </v-container>
</template>
<script>
import axios from 'axios';
import SetFormat from '@/mixins/SetFormat.vue';
import VideoListCard from '@/components/VideoListCard.vue';
import CommentList from '@/components/CommentList.vue';

export default {
  name: 'Watch',
  mixins: [SetFormat],
  data: () => ({
    show: false,
    statusModal: false,

    videos: [],
    watchVideo: {},
    videoUrl: null,
    videoLoading: false,
    type:"",
    videoId:"",
    userId:"",
    commentId:"",
    feeling:"",
    comments: [],
    text:'',
    // replies: [],
    //Replytext: '',

    tab: null,
    loading: false,

    headers: [
      { text: '작성내용', align: 'start', value: 'text' },
      { text: '작성시간', value: 'createdAt' },
      { text: 'ACT', value: 'ACT'},
    ],
  }),
  components: {
    VideoListCard,
    CommentList,
  },
  watch: {
    $route(to, from) {
      if (to.path != from.path) {
        this.getWatchData(this.$route.params.id);
      }
    },
    watchVideo() {
      this.videoUrl = `${process.env.VUE_APP_URL}/uploads/videos/${this.watchVideo.url}`;
      //console.log('first this.watchVideo', this.watchVideo);
      this.getComment();
      this.getReply();
    },
  },
  methods: {

    async getWatchData(id) {
      this.videoLoading = true;
      this.watchVideo = {};

if(id !== 'comments') {
  // console.log('1');
      await axios
        .get(process.env.VUE_APP_API + `/videos/${id}`, {
          headers: {
            Authorization: `Bearer ${localStorage.getItem('token')}`,
          },
        })
        .then((response) => {
          console.log(
            'getWatchData - response : ',
            response,
            response.data.data
          );
          this.watchVideo = response.data.data;
          this.videoLoading = false;
        })
        .catch((error) => {
          console.log('getWatchData - error : ', error);
        });
      
}
    },
    async getVideos() {
      await axios
        .get(process.env.VUE_APP_API + '/videos/public', {
          headers: {
            Authorization: `Bearer ${localStorage.getItem('token')}`,
          },
        })
        .then((response) => {
          console.log('getVideos - response : ', response, response.data.data);
          this.videos = response.data.data;
        })
        .catch((error) => {
          console.log('getVideos - error : ', error);
        });
    },

    async AddFeeling() {
      // console.log(JSON.parse(localStorage.getItem('user')).id);
      await axios
        .post(process.env.VUE_APP_API + '/feelings', {
          "type" : "like",
          "videoId" : this.watchVideo.id,
          "userId" : JSON.parse(localStorage.getItem('user')).id,
        },
        {
          headers: {
            Authorization: `Bearer ${localStorage.getItem('token')}`,
          },
        })
        .then((response) => {
          console.log('AddFeeling - response : ', response);
          this.getWatchData(this.$route.params.id);
          // this.watchVideo = response.data.data;
        })
        .catch((error) => {
          console.log('AddFeeling - error : ', error);
        })
    },

    async BadFeeling() {
    // console.log(JSON.parse(localStorage.getItem('user')).id);
    await axios
      .post(process.env.VUE_APP_API + '/feelings', {
        "type" : "dislike",
        "videoId" : this.watchVideo.id,
        "userId" : JSON.parse(localStorage.getItem('user')).id,
      },
      {
        headers: {
          Authorization: `Bearer ${localStorage.getItem('token')}`,
        },
      })
      .then((response) => {
        console.log('BadFeeling - response : ', response);
        this.getWatchData(this.$route.params.id);
        // this.watchVideo = response.data.data;
      })
      .catch((error) => {
        console.log('BadFeeling - error : ', error);
      })
  },

     async checkFeeling() {
       // console.log(JSON.parse(localStorage.getItem('user')).id);
      await axios
        .post(process.env.VUE_APP_API + '/feelings/check', {
          "videoId" : this.watchVideo.id,
          "userId" : JSON.parse(localStorage.getItem('user')).id,
        },
        {
          headers: {
            Authorization: `Bearer ${localStorage.getItem('token')}`,
          },
        })
        .then((response) => {
          console.log('AddFeeling - response : ', response);
          this.watchVideo = response.data.data;
        })
        .catch((error) => {
          console.log('AddFeeling - error : ', error);
        })
    },

     async getComment() {
      const videoId = this.watchVideo.id;
      //console.log('코멘트아이디확인',this.comments.id);
      //console.log(this.watchVideo.id);
if (videoId) {
      await axios
        .get(process.env.VUE_APP_API + '/comments/' + videoId + '/videos', {
          headers: {
            Authorization: `Bearer ${localStorage.getItem('token')}`,
          },
        })
        .then((response) => {
          console.log('getComment - response : ', response, response.data.data);
          this.comments = response.data.data;
        })
        .catch((error) => {
          console.log('getComment - error : ', error);
        });
}
    },

         async getReply() {
          //console.log(this.commentId)
          //console.log('코멘트 아이디',this.comment.id)
//       const videoId = this.watchVideo.id;
// if (videoId) {
      await axios
        .get(process.env.VUE_APP_API + '/replies', {
          headers: {
            Authorization: `Bearer ${localStorage.getItem('token')}`,
          },
        })
        .then((response) => {
          console.log('getReply - response : ', response, response.data.data);
          this.replies = response.data.data;
        })
        .catch((error) => {
          console.log('getReply - error : ', error);
        });
//}
    },

            async deleteComent(comment) {
              // console.log('코멘트아이디확인',comment.id);
            const videoId = this.watchVideo.id;
          if (videoId) {
              // console.log('del',this.watchVideo);
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
              //console.log('two', this.$route.params.id);
              this.getWatchData(videoId);
              alert('삭제되었습니다.');
            })
            .catch((error) => {
              console.log('deleteComent - error : ', error);
            });
          }
          } else return;
        },



          async submitForm() {
          //console.log('비디오 아이디 찾아',this.watchVideo.id);
          await axios
            .post(process.env.VUE_APP_API + '/comments', {
              'text' : this.text,
              'videoId' : this.watchVideo.id,
              'userId': JSON.parse(localStorage.getItem('user')).id,
            },
            {
              headers: {
                Authorization: `Bearer ${localStorage.getItem('token')}`,
              },
            })
            .then((response) => {
              console.log('submitForm - response : ', response);
              this.getWatchData(this.$route.params.id);
              //console.log('ddd',this.$route.params.id);
              this.text = null;
              //console.log('submitForm함수 끝')
            })
            .catch((error) => {
              console.log('submitForm - error : ', error);
            });
        },

        //   async AddReplychuga() {
        //   console.log('코멘트 아이디', this.comment)
        //   await axios
        //     .post(process.env.VUE_APP_API + '/replies', {
        //       'text' : this.Replytext,
        //       'commentId' : this.comment.id,
        //       'userId': JSON.parse(localStorage.getItem('user')).id,
        //     },
        //     {
        //       headers: {
        //         Authorization: `Bearer ${localStorage.getItem('token')}`,
        //       },
        //     })
        //     .then((response) => {
        //       console.log('AddReply - response : ', response);
        //       //this.getWatchData(this.$route.params.id);
        //       this.Replytext = null;
        //     })
        //     .catch((error) => {
        //       console.log('AddReply - error : ', error);
        //     });
        // },

  },
  mounted() {
    //console.log('sss',this.$route.params);
    this.getVideos();
    this.getWatchData(this.$route.params.id);
    //this.getComment();
  },
};
</script>
<style>
#Watch {
  display: grid;
  grid-template-columns: 70% 30%;
}

.videoInfo {
  box-sizing: border-box;
  padding: 15px;
  width: 100%;
}

.listSection {
  align-items: center;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  padding-left: 10px;
  width: 100%;
}
.listContainer {
  align-items: center;
  box-sizing: border-box;
  row-gap: 5px;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  padding-left: 10px;
  width: 100%;
}

.Feelings {
  position: relative;
  box-sizing: border-box;
  display: flex;
  justify-content: right;
}

.feelingBad, .feelingGood {
  margin: 10px;
}

.videoInfo span {
  color: rgb(116, 116, 116);
  font-size: 14px;
  margin-right: 8px;
}

.descrip {
  display: block;
  margin-top: 10px;
  margin-bottom: 10px;
}

.comment {
  padding-top: 25px;
  padding-bottom: 25px;
  padding-left: 10px;
}
.commentadd {
  margin: 10px;
}

.commentAddB {
  display: flex;
  justify-content: right;
}
</style>
