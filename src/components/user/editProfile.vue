<template>
  <div class='container_50'>
    <el-form :model='form' label-width='100px' class='sForm'>
      <el-form-item label='用户名'>
        <el-input v-model='form.username' :disabled='true'></el-input>
      </el-form-item>
      <el-form-item label='邮箱'>
        <el-input v-model='form.email' :disabled='true'></el-input>
      </el-form-item>
      <el-form-item label='性别'>
        <el-radio v-model='form.sex' label='1' class='sex'>男</el-radio>
        <el-radio v-model='form.sex' label='2' class='sex'>女</el-radio>
      </el-form-item>
      <el-form-item label='简介'>
        <el-input v-model='form.about_me'></el-input>
      </el-form-item>
      <el-button type='primary' @click='submit(form)' class='but'>提 交</el-button>
      <el-button @click='back' class='but'>返 回</el-button>
    </el-form>
  </div>
</template>

<script>
import axios from 'axios'
import store from '../../store.js'
export default {
  name: 'editProfile',
  data () {
    return {
      sharedState: store.state,
      form: {
        id: '',
        username: '',
        email: '',
        about_me: '',
        reg_since: '',
        sex: '',
        _links: {
          self: '',
          avatar: ''
        }
      }
    }
  },
  methods: {
    getUser (id) {
      const path = '/user/' + id
      axios
        .get(path)
        .then((response) => {
          if (response.status === 200) {
            this.form = response.data
          }
        })
        .catch((error) => {
          console.error(error)
        })
    },
    submit (form) {
      const id = this.sharedState.user_id
      const path = '/user/' + id
      axios
        .put(path, {
          sex: this.form.sex,
          about_me: this.form.about_me
        })
        .then((response) => {
          if (response.data === 'Success') {
            this.$message({
              // message消息提示
              message: '恭喜你，修改成功！',
              type: 'success'
            })
            this.$router.go(-1)
          }
        })
    },
    back () {
      this.$router.go(-1) // 返回上一页面
    }
  },
  created () {
    // 页面渲染后自动执行
    const userId = this.sharedState.user_id
    this.getUser(userId)
  }
}
</script>

<style lang="scss" scoped>
.sex{
  float: left;
  margin-top: 12px;
}
</style>
