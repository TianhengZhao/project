<template>
  <div v-if='posts'>
    <div
      v-for='(items, index) in posts.items'
      v-bind:key='index'
      class='post_item'
    >
      <router-link
        :to="{ name: 'hisPosts', params: { id: items.author.id } }"
      >
        <img
          v-bind:src='items.author._links.avatar'
          class='post_ava'
          alt='头像'
        />
      </router-link>
      <div class='post_top'>
        <router-link
          v-bind:to="{ name: 'post', params: { id: items.id } }"
          class='post_title'
        >
          {{ items.title }}
        </router-link>
      </div>
      <div class='post_body'>
        <vue-markdown class='post_summary' :source='items.summary'>
        </vue-markdown>
      </div>
      <div class='post_bottom'>
        <div
          class='buttons'
          v-if='items.author && items.author.id === sharedState.user_id'
        >
          <el-button
            size='mini'
            icon='el-icon-delete'
            circle
            type='danger'
            @click='del(items.id)'
          ></el-button>
        </div>
        <div class='info'>
          <i id='time' class='el-icon-time'>
            {{ $moment(items.timestamp).format('YYYY/MM/DD H:mm') }}
          </i>
          <el-divider direction='vertical'></el-divider>
          <i class='el-icon-view'>{{ items.views }}</i>
        </div>
      </div>
    </div>
    <el-pagination
      @current-change='handleCurrentChange'
      @size-change='handleSizeChange'
      :current-page='this.posts._meta.page'
      :page-size='5'
      layout='total,prev, pager, next, jumper'
      :total='this.posts._meta.total_items'
    >
    </el-pagination>
  </div>
</template>

<script>
import store from '../../store'
import axios from 'axios'
import '../../assets/bootstrap-markdown/js/bootstrap-markdown.js'
import '../../assets/bootstrap-markdown/js/bootstrap-markdown.zh.js'
import '../../assets/bootstrap-markdown/js/marked.js'
import VueMarkdown from 'vue-markdown' // 解析markdown原文为html
export default {
  name: 'followedPosts',
  components: {
    VueMarkdown
  },
  data () {
    return {
      sharedState: store.state,
      posts: '',
      id: 0,
      totalItems: 0
    }
  },
  methods: {
    getPosts () {
      console.log(this.$route)
      let page = 1
      let iid = this.sharedState.user_id
      const path = 'user/followedPosts/' + iid + '?page=' + page // 在url中添加参数
      axios.get(path).then((response) => {
        console.log(response.data)
        this.posts = response.data
        this.totalItems = this.posts._meta.total_items
      })
    },
    handleSizeChange (val) {},
    handleCurrentChange (val) {
      // 改变页码
      let iid = this.sharedState.user_id
      const path = 'user/followedPosts/' + iid + '?page=' + val // 在url中添加参数
      axios.get(path).then((response) => {
        console.log(response.data)
        this.posts = response.data
      })
    }
  },
  created () {
    this.getPosts()
  },
  beforeRouteUpdate (to, from, next) {
    next()
    this.getPosts()
  }
}
</script>
