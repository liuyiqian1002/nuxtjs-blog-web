<template>
  <div>
    <div class="header">
      <div class="response-wrap header-inner">
        <a class="logo" href="/"></a>
        <div class="nav">
          <div
            v-for="(item, index) in nav"
            :key="index"
            :class="[
              'nav-item',
              {
                'nav-item-active': navIndex === index,
              },
            ]"
            @click="jumpURL(item.router, index)"
          >
            {{ item.title }}
          </div>
          <div
            v-for="(item, index) in categoryList"
            :key="'s' + index"
            :class="[
              'nav-item',
              {
                'nav-item-active': navIndex === index + nav.length,
              },
            ]"
            @click="jumpCategory(item.id, index)"
          >
            <!-- <a class="category-links" :href="'/?category_id=' + item.id">
              {{ item.name }}</a
            > -->
            {{ item.name }}
          </div>
          <div v-if="categoryList && categoryList.length > max">
            <el-dropdown>
              <span class="el-dropdown-link">
                更多<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu slot="dropdown">
                <el-dropdown-item
                  v-for="item in categoryList.slice(max)"
                  :key="item.id"
                >
                  <a
                    class="category-links"
                    :href="'/?category_id=' + item.id"
                    >{{ item.name }}</a
                  >
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
          <a
            href="https://github.com/liuyiqian1002/nodejs-koa-blog"
            target="_blank"
            class="nav-item"
          >
            Github
          </a>
        </div>
        <div class="search">
          <el-input
            v-model="keyword"
            size="small"
            :clearable="true"
            placeholder="请输入内容"
            prefix-icon="el-icon-search"
            @keyup.enter.native="onSearch"
          >
          </el-input>
        </div>
      </div>
      <div class="avatar">
        <img
          :src="`${
            isLoginStatus
              ? 'https://cdn.qqinns.com/images/avatar_male.jpg'
              : 'https://cdn.qqinns.com/images/logo.png'
          }`"
          @click="onAvatarClick"
        />
        <span>{{ isLoginStatus ? userInfo.name : '登录' }}</span>
      </div>
    </div>
    <el-dialog
      :visible.sync="isLogin"
      width="880px"
      top="0"
      :lock-scroll="true"
      :before-close="handleClose"
      class="login-dialog"
    >
      <LoginForm @on-success="loginFormSuccess" />
    </el-dialog>
  </div>
</template>

<script>
import { mapState } from 'vuex'
import LoginForm from '@/components/common/LoginForm'

export default {
  name: 'VHeader',
  components: {
    LoginForm,
  },
  props: {
    isCategory: {
      type: Boolean,
      default: true,
    },
  },
  data() {
    return {
      keyword: '',
      navIndex: null,
      max: 5,
      nav: [
        {
          title: '首页',
          router: '/',
        },
      ],
      more: [],
      isLogin: false,
    }
  },
  computed: {
    ...mapState({
      userInfo: (state) => state.user.userInfo,
      // navIndex: (state) => state.user.navIndex,
      isLoginStatus: (state) => state.user.isLoginStatus,
      categoryList: (state) => state.category.categoryList,
    }),
  },
  watch: {
    $route: {
      handler(to, from) {
        console.log(to, from)
      },
    },
  },
  created() {
    if (process.client) {
      this.navIndex = localStorage.getItem('navIndex') || 0
    }
  },
  mounted() {
    // this.handleNav()
    this.getCategory()
    const navIndex = localStorage.getItem('navIndex')
    this.navIndex = Number(navIndex)
  },
  methods: {
    handleClose() {
      this.isLogin = false
    },
    loginFormSuccess() {
      this.isLogin = false
    },
    onAvatarClick() {
      if (this.isLoginStatus) {
        this.jumpURL('/usercenter')
      } else {
        this.isLogin = true
      }
    },
    getCategory() {
      this.$store.dispatch('category/getCategoryData')
    },
    onSearch() {
      if (!this.keyword) return false
      window.location.href = `/?keyword=${this.keyword}`
    },
    // 返回首页
    goHome() {
      window.location.href = '/'
    },
    // 跳转URL
    jumpURL(router, navIndex) {
      const { category_id, keyword } = this.$route.query
      if (category_id || keyword) {
        window.location.href = router
      } else {
        this.$router.push(router)
      }
      if (navIndex >= 0) {
        this.navIndex = navIndex
        localStorage.setItem('navIndex', navIndex)
      }
    },
    jumpCategory(categoryId, index) {
      const { protocol, host } = window.location
      window.location.href = `${protocol}//${host}/?category_id=${categoryId}`
      // this.$router.push(`/?category_id=${categoryId}`)
      const navIndex = this.nav.length + index
      // this.$store.commit('SET_NAV_INDEX', navIndex)
      this.navIndex = navIndex
      localStorage.setItem('navIndex', navIndex)
    },
  },
}
</script>

<style scoped lang="scss">
.header {
  border-bottom: 1px solid #666;
  .avatar {
    position: absolute;
    right: 80px;
    top: 8px;
    width: 100px;
    height: 40px;
    display: flex;
    align-items: center;
    cursor: pointer;
    img {
      width: 40px;
      height: 40px;
      border-radius: 50%;
      margin-right: 5px;
    }
    span {
      display: inline-block;
      width: 60px;
    }
  }
}
.header-inner {
  box-sizing: border-box;
  height: 56px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.logo {
  cursor: pointer;
  box-sizing: border-box;
  display: block;
  width: 100px;
  height: 56px;
  background: url('https://cdn.qqinns.com/images/logo1.png') center center no-repeat;
  background-size: 100px;
}
.nav {
  box-sizing: border-box;
  flex: 1;
  height: 56px;
  display: flex;
  margin: 0 64px;
  align-items: center;
}
.nav-item {
  box-sizing: border-box;
  height: 56px;
  line-height: 56px;
  padding: 0 32px;
  white-space: nowrap;
  cursor: pointer;
  display: block;
  text-align: center;
  font-size: 16px;
  color: #ccc;
  text-decoration: none;
  &-active {
    color: #2d8cf0;
  }

  &:hover {
    color: #2d8cf0;
    text-decoration: underline;
  }
}

.el-dropdown-link {
  cursor: pointer;
  font-size: 16px;
  padding: 0 32px;
  color: #222222;
  white-space: nowrap;

  &:hover {
    color: #2d8cf0;
    text-decoration: underline;
  }

  &:hover .el-icon-arrow-down {
    color: #2d8cf0;
  }
}
.el-icon-arrow-down {
  font-size: 16px;
  color: #222222;
}

.category-links {
  box-sizing: border-box;
  display: block;
  height: 100%;
  width: 100%;
  color: #222222;
  font-size: 14px;
  text-decoration: none;

  &:hover {
    color: #2d8cf0;
  }
}

.search {
  cursor: pointer;
}
.login-dialog {
  /deep/ .el-dialog {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
}

@media screen and (max-width: 540px) {
  .nav {
    display: none;
  }
  .search {
    flex: 1;
    margin-left: 24px;
  }
}
</style>
