<template>
  <div class="setting">
    <div class="setting-box zy-scroll" v-if="show.setting">
      <div class="logo"><img src="@/assets/image/logo.png"></div>
      <div class="info">
        <a @click="linkOpen('https://github.com/Hunlongyu/ZY-Player')">{{$t('website')}}</a>
        <a @click="linkOpen('https://github.com/Hunlongyu/ZY-Player/issues')">v{{pkg.version}} {{$t('issues')}}</a>
      </div>
      <div class="change">
        <div class="zy-select" @mouseleave="show.language = false">
          <div class="vs-placeholder" @click="show.language = true">{{$t('language')}}</div>
          <div class="vs-options" v-show="show.language">
            <ul>
              <li :class="s.language === i.key ? 'active' : ''" v-for="(i, j) in languages" :key="j" @click="languageClick(i.key)">{{ i.name }}</li>
            </ul>
          </div>
        </div>
        <div class="zy-select" @mouseleave="show.site = false">
          <div class="vs-placeholder" @click="show.site = true">{{$t('default_site')}}</div>
          <div class="vs-options" v-show="show.site">
            <ul>
              <li :class="s.site === i.key ? 'active' : ''" v-for="(i, j) in sites" :key="j" @click="siteClick(i.key)">{{ i.name }}</li>
            </ul>
          </div>
        </div>
      </div>
      <div class="theme">
        <div class="title">{{$t('theme')}}</div>
        <div class="theme-box">
          <div @click="changeTheme('light')" class="theme-item light">
            <div class="theme-image">
              <img src="../assets/image/light.png" alt="">
            </div>
            <div class="theme-name">Light</div>
          </div>
          <div @click="changeTheme('dark')" class="theme-item dark">
            <div class="theme-image">
              <img src="../assets/image/dark.png" alt="">
            </div>
            <div class="theme-name">Dark</div>
          </div>
          <div @click="changeTheme('green')" class="theme-item green">
            <div class="theme-image">
              <img src="../assets/image/green.png" alt="">
            </div>
            <div class="theme-name">Green</div>
          </div>
          <div @click="changeTheme('pink')" class="theme-item pink">
            <div class="theme-image">
              <img src="../assets/image/pink.png" alt="">
            </div>
            <div class="theme-name">Pink</div>
          </div>
        </div>
      </div>
      <div class="qrcode">
        <div class="title">{{$t('donate')}}</div>
        <div class="qrcode-box">
          <img class="qrcode-item" src="../assets/image/alipay.png">
          <img class="qrcode-item" src="../assets/image/wepay.jpg">
        </div>
      </div>
      <div class="clearDB">
        <span @click="clearDBEvent" class="clearBtn">{{$t('clearDB')}}</span>
        <span class="clearTips">{{$t('clearTips')}}</span>
      </div>
    </div>
  </div>
</template>
<script>
import { mapMutations } from 'vuex'
import setting from '../lib/dexie/setting'
import { sites } from '../lib/site/sites'
import db from '../lib/dexie/index'
import '../lib/cloud/index.js'
import { shell } from 'electron'
import pkg from '../../package.json'
const ipc = require('electron').ipcRenderer
export default {
  name: 'setting',
  data () {
    return {
      pkg: pkg,
      s: {},
      languages: [
        {
          key: 'zhCn',
          name: '中文'
        },
        {
          key: 'en',
          name: 'English'
        }
      ],
      sites: sites,
      show: {
        setting: false,
        language: false,
        site: false
      }
    }
  },
  computed: {
    theme: {
      get () {
        return this.$store.getters.getTheme
      },
      set (val) {
        this.SET_THEME(val)
      }
    },
    language: {
      get () {
        return this.$store.getters.getLanguage
      },
      set (val) {
        this.SET_LANGUAGE(val)
      }
    },
    site: {
      get () {
        return this.$store.getters.getSite
      },
      set (val) {
        this.SET_SITE(val)
      }
    }
  },
  methods: {
    ...mapMutations(['SET_THEME', 'SET_LANGUAGE', 'SET_SITE']),
    linkOpen (e) {
      shell.openExternal(e)
    },
    languageClick (e) {
      this.language = e
      this.show.language = false
      this.$i18n.locale = e
      this.s.language = e
      setting.update(this.s).then(res => {
        this.$m.success(this.$t('set_success'))
      })
    },
    siteClick (e) {
      this.site = e
      this.show.site = false
      this.s.site = e
      setting.update(this.s).then(res => {
        this.$m.success(this.$t('set_success'))
      })
    },
    changeTheme (e) {
      this.theme = e
      this.s.theme = e
      setting.update(this.s).then(res => {
        this.$m.success(this.$t('set_success'))
      })
    },
    clearDBEvent () {
      db.delete().then(res => {
        this.$m.success(this.$t('set_success'))
        ipc.send('close')
      })
    }
  },
  created () {
    setting.find().then(res => {
      this.s = res
      this.theme = res.theme
      this.$i18n.locale = this.s.language
      this.show.setting = true
    })
  }
}
</script>
<style lang="scss" scoped>
.setting{
  height: calc(100% - 40px);
  width: 100%;
  padding: 20px 0;
  .setting-box{
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    border-radius: 5px;
    overflow-y: auto;
  }
 .logo{
    margin-top: 10px;
    width: 100%;
    text-align: center;
    img{
      width: 120px;
      height: auto;
    }
  }
  .info{
    width: 100%;
    margin-top: 20px;
    text-align: center;
    a{
      text-decoration: none;
      margin: 0 10px;
      font-size: 14px;
      cursor: pointer;
    }
  }
  .change{
    width: 100%;
    display: flex;
    justify-content: flex-start;
    padding-left: 20px;
    margin-top: 40px;
    .zy-select{
      margin-right: 20px;
    }
  }
  .theme{
    width: 100%;
    padding-left: 20px;
    margin-top: 20px;
    .theme-box{
      display: flex;
      justify-content: flex-start;
      margin-top: 10px;
      .theme-item{
        width: 200px;
        height: 180px;
        margin-right: 20px;
        cursor: pointer;
        border-radius: 2px;
        .theme-image{
          width: 180px;
          margin: 10px auto;
          img{
            width: 100%;
          }
        }
        .theme-name{
          width: 100%;
          text-align: center;
        }
      }
    }
  }
  .qrcode{
    width: 100%;
    padding-left: 20px;
    margin-top: 20px;
    margin-bottom: 20px;
    .qrcode-box{
      display: flex;
      justify-content: flex-start;
      margin-top: 10px;
      .qrcode-item{
        width: auto;
        height: 300px;
        margin-right: 20px;
        border-radius: 2px;
      }
    }
  }
  .clearDB{
    margin-top: 20px;
    margin-bottom: 20px;
    .clearBtn{
      margin-left: 20px;
      color: red;
      cursor: pointer;
      border: 1px solid #ff000088;
      display: inline-block;
      width: 160px;
      height: 32px;
      font-size: 14px;
      text-align: center;
      line-height: 32px;
    }
    .clearTips{
      font-size: 12px;
      color: #ff000088;
      margin-left: 10px;
    }
  }
}
</style>
