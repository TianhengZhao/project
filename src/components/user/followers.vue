<template>
  <div v-if='fans'>
    <div class='total'>
      <span class='el-icon-user'>共{{ this.totalItems }}位粉丝</span>
    </div>
    <div v-for='(items, index) in fans.items' v-bind:key='index' class='f_wrap'>
      <router-link v-bind:to="{ name: 'hisPosts', params: { id: items.id } }">
        <img v-bind:src='items._links.avatar' class='f_ava' alt='头像' />
      </router-link>
      <div class='f_top'>
        <router-link
          v-bind:to="{ name: 'hisPosts', params: { id: items.id } }"
          class='f_name'
        >
          {{ items.username }}
        </router-link>
      </div>
      <div class='f_about'>{{ items.about_me }}</div>
      <el-button
        v-if='items.id !== sharedState.user_id && !items.is_following'
        class='f_button'
        type='primary'
        round
        size='mini'
        @click='follow(items.id, items.username)'
      >
        添加关注
      </el-button>
      <el-button
        v-else-if='items.id !== sharedState.user_id && items.is_following'
        class='f_button'
        type='danger'
        round
        size='mini'
        @click='unfollow(items.id, items.username)'
      >
        取消关注
      </el-button>
    </div>
    <el-pagination
      @current-change='handleCurrentChange'
      @size-change='handleSizeChange'
      :current-page='this.fans._meta.page'
      :page-size='4'
      layout='total,prev, pager, next, jumper'
      :total='this.fans._meta.total_items'
    >
    </el-pagination>
  </div>
</template>

<script>
import store from '../../store'
import axios from 'axios'
export default {
  name: 'followers',
  data () {
    return {
      sharedState: store.state,
      fans: '',
      id: 0,
      totalItems: 0
    }
  },
  methods: {
    getFans () {
      let page = 1
      let iid = this.$route.params.id
      const path = 'user/getOnesFans/' + iid + '?page=' + page // 在url中添加参数
      axios.get(path).then((response) => {
        console.log(response.data)
        this.fans = response.data
        this.totalItems = this.fans._meta.total_items
      })
    },
    handleSizeChange (val) {},
    handleCurrentChange (val) {
      // 改变页码
      let iid = this.$route.params.id
      const path = 'user/getOnesFans/' + iid + '?page=' + val // 在url中添加参数
      axios.get(path).then((response) => {
        console.log(response.data)
        this.fans = response.data
      })
    },
    follow (id, name) {
      const path = '/user/follow/' + id
      axios
        .get(path)
        .then((response) => {
          if (response.data === 'Success') {
            this.$message({
              type: 'success',
              message: '已成功关注' + name + '!',
            })
            this.getFans() // 关注后刷新生成新的粉丝列表
          } else {
            this.$message.error('关注失败！')
          }
        })
        .catch((error) => {
          console.error(error)
        })
    },
    unfollow (id, name) {
      const path = '/user/unfollow/' + id
      axios
        .get(path)
        .then((response) => {
          if (response.data === 'Success') {
            this.$message({
              type: 'success',
              message: '已取消关注' + name + '!',
            })
            this.getFans()
          } else {
            this.$message.error('取消关注失败！')
          }
        })
        .catch((error) => {
          console.error(error)
        })
    }
  },
  created () {
    this.getFans()
  }
}
</script>

<style lang="scss" scoped>
@import "../../assets/style/follow.scss";
</style>
