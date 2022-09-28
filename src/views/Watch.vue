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
              <v-text-field placeholder="댓글 추가하기" @click="openCommentModal">
              </v-text-field>
              <!-- <v-btn class="commentchuga" @click="openCommentModal">댓글 등록</v-btn> -->

        <v-tabs class="mt-5" v-model="tab">
          <v-tab>댓글 리스트</v-tab>
        </v-tabs>
        <v-tabs-items v-model="tab">
      <!-- 새로만든거 -->
      <v-tab-item>
      <v-data-table  :headers="headers" :items="comments" >
      <template v-slot:item="{ item }">
        <tr>
          <td>{{ item.text }}</td>
          <td>{{ setCalDate(item.createdAt) }}</td>
          <td>
            <v-btn icon @click="deleteComent(item)">
              <v-icon>mdi-delete-forever</v-icon>
            </v-btn>
          </td>
        </tr>
      </template>
      </v-data-table>
      </v-tab-item>
      <CommentModal
      :openDialog="statusModal"
      v-on:closeDialog="closeCommentModal" />
    </v-tabs-items>
          
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
import CommentModal from '@/components/Modal/CommentModal.vue';

export default {
  name: 'Watch',
  mixins: [SetFormat],
  data: () => ({
    statusModal: false,

    videos: [],
    watchVideo: {},
    videoUrl: null,
    videoLoading: false,
    type:"",
    videoId:"",
    userId:"",
    feeling:"",
    comments: [],

    tab: null,
    loading: false,

    headers: [
      { text: '작성내용', align: 'start', value: 'text' },
      { text: '작성시간', value: 'createdAt' },
      { text: 'ACT', value: 'ACT'},
    ],
  }),
  components: {
    CommentModal,
    VideoListCard,
  },
  watch: {
    $route(to, from) {
      if (to.path != from.path) {
        this.getWatchData(this.$route.params.id);
      }
    },
    watchVideo() {
      this.videoUrl = `${process.env.VUE_APP_URL}/uploads/videos/${this.watchVideo.url}`;
      // console.log('first this.watchVideo', this.watchVideo);
      this.getComment();
    },
  },
  methods: {
    openCommentModal() {
      this.statusModal = true;
      console.log('-- open : ', this.statusModal);
    },
    closeCommentModal() {
      this.statusModal = false;
      console.log('-- close : ', this.statusModal);
    },

    async getWatchData(id) {
      //console.log(this.$route.params.id);
      this.videoLoading = true;
      this.watchVideo = {};

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
              this.getCategories();
            })
            .catch((error) => {
              console.log('deleteComent - error : ', error);
            });
          } else return;
        },
  },
  mounted() {
    this.getVideos();
    this.getWatchData(this.$route.params.id);
    this.getComment();
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
}

</style>
