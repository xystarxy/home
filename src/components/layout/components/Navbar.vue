<template>
  <div class="navbar">
    <div class="left-menu">
      <hamburger
        :is-active="sidebar.opened"
        class="hamburger-container"
        @toggleClick="toggleSideBar"
      />
      <div class="system-container">
        <div class="system-left">
          <el-dropdown @command="changeSys">
            <span class="sys-change el-dropdown-link pointer">
              <span class="name">主控台 </span>
              <span class="icon">
                <i class="el-icon-arrow-down el-icon--right" />
              </span>
            </span>
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item
                v-for="(item, index) in sysList"
                :key="index"
                style="padding: 5px 10px"
                :command="item.link"
              >
                {{ item.name }}
              </el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
        </div>
        <div class="system-right" @click="OpenBI">
          <span>BI系统</span>
          <i class="el-icon-right"></i>
        </div>
      </div>
      <breadcrumb />
    </div>
    <div v-show="false">
      <iframe  ref="iframe" :src="url" width="100%" :height="ifreameHeight" frameborder="0" scrolling="auto" id="iframename" name="iframename"></iframe>
    </div>
    <div class="right-menu">
      <el-dropdown class="avatar-container" trigger="click">
        <div class="avatar-wrapper">
          <!-- <img :src="avatar+'?imageView2/1/w/80/h/80'" class="user-avatar"> -->
          <img :src="avatar" :alt="Name" :title="Name" class="user-avatar" />
          <!--{{ Name }} -->
          <!--<i class="el-icon-caret-bottom" /> -->
        </div>
        <el-dropdown-menu slot="dropdown" class="user-dropdown">
          <!-- <router-link to="/usercenter">
            <el-dropdown-item>
              个人中心
            </el-dropdown-item>
          </router-link> -->
          <el-dropdown-item divided @click.native="logout">
            <span style="display: block">退出</span>
          </el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
    </div>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import Breadcrumb from "@/components/Breadcrumb";
import Hamburger from "@/components/Hamburger";
import usercenterVue from "../../views/user/usercenter.vue";
import { getUserSystems } from "@/api/user";
import { logout } from '@/views/callBack/is4.js'

export default {
  components: {
    Breadcrumb,
    Hamburger,
  },
  data() {
    return {
      url:"",
      Name: this.$store.getters.name,
      avatar: require("@/assets/images/avatar.png"),
      FRAddress: process.env.VUE_APP_FR_ADDRESS,
      sysList: [],
    };
  },
  computed: {
    ...mapGetters([
      "sidebar",
      //'avatar'
    ]),
  },
  async created() {
    this.url = this.FRAddress + "/webroot/decision?ssoToken="+this.$store.getters.fr_token;
    this.getUserSystem();
  },
  methods: {
    async getUserSystem() {
      const res = await getUserSystems();
      // 获取系统
      if (res) {
        this.sysList = [];
        const homePath = {
          name: "主控台",
          link: `${process.env.VUE_APP_MAIN}/#/home`,
        };
        this.sysList = res.filter((item) => item.hasPermission);
        this.sysList.unshift(homePath);
      }
    },
    // 系统切换方法
    changeSys(path) {
      window.location = path;
    },
    toggleSideBar() {
      this.$store.dispatch("app/toggleSideBar");
    },
    async logout() {
      await this.$store.dispatch("user/logout");
      logout()
    },
    //打开bi系统
    async OpenBI() {
      let FRBIAddress =
        this.FRAddress +
        "webroot/decision";
        //"?bcuser=" +
        //this.$store.getters.name +
        //"&bctoken=" +
        //this.$store.getters.fr_token;
      window.open(FRBIAddress);
    },
    async usercenter() {
      await this.$store.dispatch("user/usercenter");
    },
  },
};
</script>

<style lang="scss" scoped>
.user-dropdown {
  top: 40px !important;
}
.navbar {
  height: 50px;
  overflow: hidden;
  position: relative;
  background: #0088ff;
  box-shadow: 0 1px 4px rgba(0, 21, 41, 0.08);
  .left-menu {
    width: 50%;
    height: 100%;
    display: flex;
    float: left;
    align-items: center;
    .hamburger-container {
      cursor: pointer;
      transition: background 0.3s;
      -webkit-tap-highlight-color: transparent;
      &:hover {
        background: rgba(0, 0, 0, 0.025);
      }
    }
    .system-container {
      display: flex;
      .system-left {
        margin-left: 5px;
        .sys-change {
          width: 80px;
          height: 22px;
          display: inline-block;
          display: flex;
          justify-content: space-between;
          align-items: center;
          outline: none;

          span.name {
            height: 22px;
            line-height: 22px;
            border-radius: 2px;
            background: #fff;
            font-size: 12px;
            color: #0088ff;
            flex: 1;
            text-align: center;
          }
          span.icon {
            width: 18px;
            height: 100%;
            justify-content: center;
            display: flex;
            align-items: center;
            i {
              font-size: 12px;
              color: #fff;
              transform: scale(0.8);
              margin: 0;
            }
          }
        }
      }
      .system-right {
        cursor: pointer;
        margin: 0 20px;
        span {
          height: 22px;
          display: inline-block;
          border-radius: 2px;
          line-height: 22px;
          background: #fff;
          color: #0088ff;
          padding: 0 10px;
          font-size: 12px;
        }
        i {
          color: #fff;
          font-size: 12px;
        }
      }
    }
  }
  .right-menu {
    float: right;
    height: 100%;
    line-height: 50px;

    &:focus {
      outline: none;
    }

    .right-menu-item {
      display: inline-block;
      padding: 0 8px;
      height: 100%;
      font-size: 18px;
      color: #5a5e66;
      vertical-align: text-bottom;

      &.hover-effect {
        cursor: pointer;
        transition: background 0.3s;

        &:hover {
          background: rgba(0, 0, 0, 0.025);
        }
      }
    }

    .avatar-container {
      margin-right: 30px;

      .avatar-wrapper {
        margin-top: 5px;
        position: relative;

        .user-avatar {
          cursor: pointer;
          width: 40px;
          height: 40px;
          border-radius: 10px;
        }
        .el-icon-caret-bottom {
          cursor: pointer;
          position: absolute;
          right: -20px;
          top: 25px;
          font-size: 12px;
        }
      }
    }
  }
}
</style>
