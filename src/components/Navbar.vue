<template>
<div id="Navbar" v-if="!isHomePage">
  <Menu @on-select="gotoHomePage" mode="horizontal" theme="light" :active-name="currentPage">
    <Row>
      <Col span="12">
      <router-link to="/">
        <Menu-item id="brand" name="brand">
          刷题吧！
        </Menu-item>
      </router-link>
      <router-link to="/">
        <Menu-item name="home">
          首页
        </Menu-item>
      </router-link>
      <router-link to="/exam/normal">
        <Menu-item name="exam">
          刷题
        </Menu-item>
      </router-link>
      <router-link to="/collection">
        <Menu-item name="collection">
          题目收藏
        </Menu-item>
      </router-link>

      </Col>
      <Col span="4" offset="8" v-if="!isLogged">
      <router-link to="/login">
        <Menu-item name="login">
          <!-- <Button type="text" size="large" @click="toggleLogged">假登陆</Button> -->
          登陆
        </Menu-item>
      </router-link>
      <router-link to="/register">
        <Menu-item name="register">
          注册
        </Menu-item>
      </router-link>
      </Col>

      <Col span="4" offset="8" v-if="isLogged">
      <!-- //TBD resiponsive+ -->
      <Menu-item name="4" id="userItem">
        <Dropdown style="margin-left: 20px">
          <Button type="primary">
          {{name}}
          </Button>
          <DropdownMenu slot="list">
            <DropdownItem><a @click="toggleLogout" href="javascript:void(0)">登出</a></DropdownItem>
          </DropdownMenu>
        </Dropdown>
      </Menu-item>
      </Col>
    </Row>
  </Menu>
</div>
</template>

<script>
import utils from '@/api/utils'

import {
  mapState,
  mapMutations
} from 'vuex'

export default {
  name: "Navbar",
  data: () => ({}),

  computed: {
    ...mapState([
      'isLogged',
      'currentPage'
    ]),
    isHomePage() {
      return (this.currentPage === "home")
    },
    name() {
      return this.$store.state.user.name;
    }
  },

  methods: {
    ...mapMutations([
      'toggleLogged',
      'toggleHomePage'
    ]),
    gotoHomePage(name) {
      if (name === "0" || name === "1") {
        this.toggleHomePage();
        console.log('click' + name);
      }
    },
    toggleLogout() {
        this.toggleLogged();
        utils.logout(this.name);
    }
  }
}
</script>

<style lang="css" scoped>
#Navbar{
    margin-bottom: 0;
}

#brand{
    border: 0;
    font-size: 16px;
    color: #464c5b;
    font-family: "Hiragino Sans GB";
    font-weight: bold;
}

#userItem{
    /*border: 0;*/
    border-color: rgba(255, 0, 0, 0.01);
}
</style>
