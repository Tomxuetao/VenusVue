<template>
    <div class="site-wrapper site-page--login">
        <div class="site-content__wrapper">
            <div class="site-content">
                <div class="brand-info">
                    <h2 class="brand-info__text">renren-fast-vue</h2>
                    <p class="brand-info__intro">
                        renren-fast-vue基于vue、element-ui构建开发，实现renren-fast后台管理前端功能，提供一套更优的前端解决方案。</p>
                </div>
                <div class="login-main">
                    <h3 class="login-title">管理员登录</h3>
                    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
                             status-icon>
                        <el-form-item prop="userName">
                            <el-input v-model="dataForm.userName" placeholder="帐号"></el-input>
                        </el-form-item>
                        <el-form-item prop="password">
                            <el-input v-model="dataForm.password" type="password" placeholder="密码"></el-input>
                        </el-form-item>
                        <el-form-item prop="captcha">
                            <el-row :gutter="20">
                                <el-col :span="14">
                                    <el-input v-model="dataForm.captcha" placeholder="验证码">
                                    </el-input>
                                </el-col>
                                <el-col :span="10" class="login-captcha">
                                    <img :src="captchaPath" @click="getCaptcha()" alt="">
                                </el-col>
                            </el-row>
                        </el-form-item>
                        <el-form-item>
                            <el-button class="login-btn-submit" type="primary" @click="dataFormSubmit()">登录</el-button>
                        </el-form-item>
                    </el-form>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { getUUID } from '@/utils'

export default {
    data () {
        return {
            dataForm: {
                userName: '',
                password: '',
                uuid: '',
                captcha: ''
            },
            dataRule: {
                userName: [
                    { required: true, message: '帐号不能为空', trigger: 'blur' }
                ],
                password: [
                    { required: true, message: '密码不能为空', trigger: 'blur' }
                ],
                captcha: [
                    { required: true, message: '验证码不能为空', trigger: 'blur' }
                ]
            },
            captchaPath: ''
        }
    },
    created () {
        this.getCaptcha()
    },
    methods: {
        // 提交表单
        dataFormSubmit () {
            this.$refs['dataForm'].validate((valid) => {
                if (valid) {
                    this.$http({
                        url: this.$http.adornUrl('/sys/login'),
                        method: 'post',
                        data: this.$http.adornData({
                            'username': this.dataForm.userName,
                            'password': this.dataForm.password,
                            'uuid': this.dataForm.uuid,
                            'captcha': this.dataForm.captcha
                        })
                    }).then(({ data }) => {
                        if (data && data.code === 0) {
                            this.$cookie.set('token', data.token)
                            this.$router.replace({ name: 'home' })
                        } else {
                            this.getCaptcha()
                            this.$message.error(data.msg)
                        }
                    })
                }
            })
        },
        // 获取验证码
        getCaptcha () {
            this.dataForm.uuid = getUUID()
            this.captchaPath = this.$http.adornUrl(`/captcha.jpg?uuid=${this.dataForm.uuid}`)
        }
    }
}
</script>

<style lang="scss">
    @import '~@/assets/scss/modules/sys/login.scss';
</style>
