<template>
  <div class="login-container">
    <div class="form-warp">
      <h2 class="header">嗨购后台管理系统</h2>
      <el-form
        ref="ruleFormRef"
        :model="ruleForm"
        status-icon
        :rules="rules"
        class="demo-ruleForm"
      >
        <el-form-item prop="adminname">
          <el-input
            placeholder="输入用户名"
            v-model="ruleForm.adminname"
            autocomplete="off"
          >
            <template #prefix>
              <el-icon color="#409EFC" class="no-inherit">
                <User />
              </el-icon>
            </template>
          </el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input
            placeholder="输入密码"
            v-model="ruleForm.password"
            autocomplete="off"
            type="password"
          >
            <template #prefix>
              <el-icon color="#409EFC" class="no-inherit">
                <Lock />
              </el-icon>
            </template>
          </el-input>
        </el-form-item>
        <el-form-item prop="confirmPassword">
          <el-input
            placeholder="输入确认密码"
            v-model="ruleForm.confirmPassword"
            autocomplete="off"
            type="password"
          >
            <template #prefix>
              <el-icon color="#409EFC" class="no-inherit">
                <Lock />
              </el-icon>
            </template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="submitForm(ruleFormRef)"
            >登录</el-button
          >
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
import { defineComponent } from 'vue'
import { User, Lock } from '@element-plus/icons-vue'
import { login } from '@/api/user'
import { mapMutations, mapState } from 'vuex'
export default defineComponent({
  components: {
    User,
    Lock
  },
  data() {
    return {
      ruleFormRef: null,
      ruleForm: {
        adminname: '',
        password: '',
        confirmPassword: ''
      },
      rules: {
        adminname: [{ required: true, trigger: 'blur' }],
        password: [{ required: true, trigger: 'blur' }],
        confirmPassword: [
          { required: true, trigger: 'blur', validator: this.validatorPwd }
        ]
      }
    }
  },
  mounted() {
    this.ruleFormRef = this.$refs.ruleFormRef // 组件挂载完毕的时候，this.ruleFormRef = form表单组件
  },
  computed: {
    ...mapState(['currentPath'])
  },
  methods: {
    ...mapMutations(['updateUserInfo']),
    validatorPwd(rule, value, callback) {
      if (value !== this.ruleForm.password) {
        callback(new Error('两次密码输入不一致!'))
      } else {
        callback()
      }
    },
    submitForm(ruleFormRef) {
      if (!ruleFormRef) return
      ruleFormRef.validate((valid) => {
        if (valid) {
          login({
            adminname: this.ruleForm.adminname,
            password: this.ruleForm.password
          }).then((res) => {
            // 1.登录成功后，需要保存用户信息
            // 2.如果用户登录了，不能重复登录
            // 3.编程式导航跳转到home首页
            this.updateUserInfo(res.data)
            this.$router.push(this.currentPath || '/')
          })
        } else {
          console.log('error submit!')
          return false
        }
      })
    }
  }
})
</script>

<style lang="scss" scoped>
.login-container {
  height: 100%;
  background: #2b3c4d;
  .form-warp {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    .header {
      color: #fff;
      text-align: center;
      margin-bottom: 32px;
    }
    .el-form {
      background: #fff;
      width: 320px;
      padding: 32px 32px 20px 32px;
      border-radius: 10px;
      .el-button {
        width: 100%;
        margin-top: 24px;
      }
    }
  }
}
</style>
