<template>
    <div class='d-flex'>
      <div>
      <v-sheet 
        outlined
        class='mx-0'
        height='100vh'
        width='600'
        >
          <v-container class='d-flex justify-space-between'>
            <div class='text-h6 font-weight-bold'>Home</div>
            <v-tooltip bottom>
              <template v-slot:activator='{ on, attrs }'>
                <v-btn icon
                v-bind='attrs'
                v-on='on'>
                  <v-icon>auto_awesome</v-icon>
                </v-btn>
              </template>
              <span>Top Tweets</span>
            </v-tooltip>
          </v-container>
          <v-divider></v-divider>
          
          <v-virtual-scroll v-if="this.tweets" :items='tweets' height='90vh' item-height='153' bench='6' class='scrollbar-hidden'>
            <template v-slot:default="{ item }">
              <Tweet :tweet="item"/>
            </template>
          </v-virtual-scroll>

          <h1 v-else>Loading...</h1>
        </v-sheet>
      </div>

    <div style='width: 100%;'>
      <search-container></search-container>
    </div>

  </div>

</template>

<script>
import SearchContainer from '../components/SearchContainer.vue'
import Tweet from './Tweet.vue'

import {makeRequest} from '../utils/makeRequest'

export default {
  name: 'PostContainer',
  components: {SearchContainer, Tweet},
  created: function(){
    this.fetchPosts();
  },
  data: () => ({
    tweets: null
  }),
  methods: {
    async getUsersData(){
      const user = await makeRequest('http://localhost:3000/users/', 'get', '');
      return user
    },
    async mapUsersToPosts(posts){
      const users = await this.getUsersData()
      return posts.map(post=>({
        ...post,
        user: users.find(user => user.id === post.userId)
      }))
    },
    fetchPosts(){
      makeRequest('http://localhost:3000/posts', 'get', '').then(response => {
        this.mapUsersToPosts(response).then(posts => {
        this.tweets = posts
        })
      })
    }

  }
}
</script>

<style>
.scrollbar-hidden::-webkit-scrollbar {
  display: none;
}
.scrollbar-hidden {
  -ms-overflow-style: none;
  scrollbar-width: none;
}
</style>