<template>
<div id="Register">
  <div class="centerPage">
    <Card style="width:350px" align="left">
      <div slot="title">

        <h2><Icon type="person-add"></Icon> 注册</h2>
      </div>
      <a slot="extra" @click.prevent="">
        <router-link to="/login">
          <Icon type="arrow-right-b"></Icon>
          登陆
        </router-link>
      </a>
      <Form ref="form" :model="form" :rules="rule">
        <FormItem prop="username">
          <Input type="text" v-model="form.username" placeholder="用户名">
          <Icon type="ios-person-outline" slot="prepend"></Icon>
          </Input>
        </FormItem>
        <FormItem prop="useremail" id="chaptchaBtn">
          <Input type="text" v-model="form.useremail" placeholder="邮箱">
          <Icon type="ios-email-outline" slot="prepend"></Icon>
          </Input>
          <a type="text" @click="sendEmail">发送验证码</a>
        </FormItem>
        <FormItem prop="chaptcha">
          <Input type="text" v-model="form.chaptcha" placeholder="验证码">
          <Icon type="ios-ionic-outline" slot="prepend"></Icon>
          </Input>
        </FormItem>
        <FormItem prop="password">
          <Input type="password" v-model="form.password" placeholder="密码">
          <Icon type="ios-locked-outline" slot="prepend"></Icon>
          </Input>
        </FormItem>
        <FormItem prop="passwordcheck">
          <Input type="password" v-model="form.passwordcheck" placeholder="再次输入密码">
          <Icon type="ios-locked-outline" slot="prepend"></Icon>
          </Input>
        </FormItem>
        <FormItem>
          <Button type="primary" long @click="handleSubmit('form')">注册</Button>
        </FormItem>
      </Form>
    </Card>
  </div>
  <vue-particles color="#dedede"></vue-particles>
</div>
</template>
<script>
import {
  mapMutations
} from 'vuex';

import utils from '@/api/utils'

import axios from '@/axios';

export default {
  name: "Register",
  data() {
    /**
     * check password
     * ref:https://www.iviewui.com/docs/guide/install
     */
    const validatePassCheck = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请您再次输入密码'));
      } else if (value !== this.form.password) {
        callback(new Error('两次输入的密码不匹配'));
      } else {
        callback();
      }
    };
    return {
      form: {
        username: '',
        useremail: '',
        chaptcha: '',
        password: '',
        passwordcheck: ''
      },
      rule: {
        username: [{
          required: true,
          message: '请填写用户名',
          trigger: 'blur'
        }, {
          pattern: /^[\u4E00-\u9FA5A-Za-z0-9_]+$/,
          message: '请不要输入^%&\',;=?$\\"等字符',
          trigger: 'blur'
        }],
        useremail: [{
          required: true,
          message: '请填写邮箱',
          trigger: 'blur'
        }, , {
          type: 'email',
          message: '请输入正确的邮箱地址',
          trigger: 'blur'
        }],
        password: [{
            required: true,
            message: '请输入密码',
            trigger: 'blur'
          },
          {
            type: 'string',
            min: 8,
            max: 30,
            message: '请输入8～30位的密码',
            trigger: 'blur'
          }
        ],
        chaptcha: [{
          required: true,
          message: '请填写验证码',
          trigger: 'blur'
        }],
        passwordcheck: [{
          validator: validatePassCheck,
          trigger: 'blur'
        }]
      }
    }
  },

  /**
   * set current page to this page
   */
  beforeRouteEnter: function (to, from, next) {
    console.log('registerok');
    next(Register => {
      Register.setCurrentPage('register');
    });
  },

  methods: {
      /**
       * 没有用到util，直接复制了过来
       * @param  {[type]} name [description]
       * @return {[type]}      [description]
       */
    handleSubmit(name) {
      this.$refs[name].validate((valid) => {
        if (valid) {

          let pack = {
            email: this.form.useremail,
            username: this.form.username,
            password: this.form.password,
            validate: this.form.chaptcha
          }

          axios({
            method: 'post',
            url: '/user/register/',
            data: pack
          }).then(response => {
            let name = response.data.username;
            let success = response.data.success;
            let err = response.data.err_msg;
            if (success) {
              this.$Message.success('注册成功');
            } else {
              this.$Message.error('注册失败!'+err);
            }
            console.log(response);
          }).catch(e => {
            console.log(e);
          });
        } else {
          this.$Message.error('请输入正确的信息!');
        }
      })
    },
    ...mapMutations([
      'setCurrentPage',
    ]),
    /**
     * 没有用到util，直接复制了过来
     * @return {[type]} [description]
     */
    sendEmail() {
      axios({
        method: 'post',
        url: '/user/sendEmail/',
        data: {
          email: this.form.useremail
        }
      }).then(response => {
        let success = response.data.success;
        if (success) {
          this.$Message.success('发送成功!');
        } else {
          this.$Message.error('发送失败!');
        }
        console.log(response);
      }).catch(e => {
        console.log(e);
      });
    }
  }
}
</script>
<style lang="css" scoped>
#chaptchaBtn{
    /*margin-bottom: 0;
    padding-bottom: 0;
    border-bottom: 0;*/
}
</style>
