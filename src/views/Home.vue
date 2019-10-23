<template>
  <div class="contaner">
    <div class="userWrapper">
      <span class="tltle">{{ $t(`friendsList`) }}({{ userList.length }})</span>
      <ul class="userList">
        <li class="user" v-for="(user,user_ind) in userList" :key="user_ind" @click="nowSel = user_ind">
          <div class="userImg"></div>
          <div class="userInfo">
            <span class="userNane">{{ user.name }}</span>
            <span class="userMessage">大家好，我是{{ user.name }}～</span>
          </div>
        </li>
      </ul>
    </div>
    <div class="MessageWrapper">
      <div class="header">
        <div class="navBar">
          <span class="title">Cherri Chat</span>
          <div class="language">
            <div :class="['langBtn', lang === 'tw'?'active':'']" @click="lang ='tw'">中文</div>
            <div :class="['langBtn', lang === 'en'?'active':'']" @click="lang ='en'">English</div>
          </div>
        </div>
        <div class="loginUser">
          <div class="userImg"></div>
          <span class="userName">潔西卡</span>
        </div>
      </div>
      <div class="welcomeWrapper" v-if="nowSel == null">
        <div class="imgBox">
          <img src="../assets/img_default.png" alt="">
          <p>開始使用Cherri Chat!</p>
        </div>
      </div>
      <div class="message" v-if="nowSel != null">
        <div class="userBar">
          <div class="userInfo">
            <div class="userImg"></div>
            <div class="userName">{{ userList[nowSel].name }}</div>
          </div>
          <div class="option">
            <div :class="[suchShow? 'active':'','such']" @click="suchShow =! suchShow"><img src="../assets/ic_search.png" alt=""></div>
            <div :class="[noteShow? 'active':'','note']" @click="noteShow =! noteShow"><img src="../assets/ic_note.png" alt=""></div>
          </div>
        </div>
        <div class="messageContent">
          <div class="suchWrapper" v-if="suchShow">
            <input id="suchContentInput" type="text" @keydown.enter="such">
            <span v-if="suchContent != ''">{{ messageData().count }} {{ $t(`items`) }}</span>
            <div v-if="suchContent != ''" class="closeBtn" @click="cleanSuch()"></div>
          </div>
          <div class="noteWrapper" v-if="noteShow">
            <textarea :placeholder="$t(`message`) + '...'" cols="30" rows="3" v-model="noteContent"></textarea>
            <div class="sandBtn" @click="addNote()">{{ $t(`add`) }}</div>
            <ul>
              <li v-for="(note,note_ind) in userList[nowSel].note" :key="note_ind">
                <div class="delBtn" @click="delNote(note_ind)"></div>
                <span class="dateTime">{{ note.datetime }}</span>
                <p class="note">{{ note.note }}</p>
              </li>
            </ul>
          </div>
          <ul class="msgWrapper">
            <li v-for="(msg,msg_ind) in messageData().msg" :key="msg_ind"><span class="msg" v-html="msg"></span></li>
          </ul>
        </div>
        <div class="MessageInput">
          <input type="text" name="" id="" :placeholder="$t(`message`) + '...'">
          <div class="sendBtn"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import i18n from '../lang/lang'
export default {
  name: 'home',
  components: {
  },
  data () {
    return {
      lang: 'tw',
      nowSel: null,
      suchShow: false,
      suchContent: '',
      noteShow: false,
      noteContent: '',
      userList: [{
        name: '保羅',
        msg: ['保羅', '你好,我是傑西卡', '我喜歡吃的食物有', '各種巧克力口味的甜點'],
        note: []
      },
      {
        name: '傑克',
        msg: ['傑克', '你好, 我是傑西卡', '我喜歡做的運動為', '游泳,跑步'],
        note: []
      },
      {
        name: '傑森',
        msg: ['傑森', '你好, 我是傑西卡', '我喜歡的動物為 ', '貓,狗'],
        note: []
      }]
    }
  },
  watch: {
    nowSel () {
      this.suchShow = false
      this.noteShow = false
    },
    suchShow (v) {
      if (v) {
        this.$nextTick(() => {
          document.getElementById('suchContentInput').focus()
        })
      }
      this.suchContent = ''
    },
    noteShow () {
      this.noteContent = ''
    },
    lang (v) {
      this.changeLanguage(v)
    }
  },
  methods: {
    addNote () {
      let getDate = new Date()
      let nowdate = (getDate.getFullYear()) + '/' + ('0' + (getDate.getMonth() + 1)).slice(-2) + '/' + ('0' + getDate.getDate()).slice(-2) + ' ' + getDate.getHours() + ':' + ('0' + getDate.getMinutes()).slice(-2) + ':' + ('0' + getDate.getSeconds()).slice(-2)
      this.userList[this.nowSel].note.unshift({ datetime: nowdate, note: this.noteContent })
      this.noteContent = ''
    },
    delNote (ind) {
      this.userList[this.nowSel].note.splice(ind, 1)
    },
    messageData () {
      if (this.suchContent !== '') {
        let count = 0
        let suchData = this.userList[this.nowSel].msg.map(v => {
          if (v.indexOf(this.suchContent) !== -1) { count++ }
          return v.replace(new RegExp(this.suchContent, 'i'), '<span class=highlight>' + this.suchContent + '</span>')
        })
        return { msg: suchData, count: count }
      } else {
        return { msg: this.userList[this.nowSel].msg, count: null }
      }
    },
    such (el) {
      this.suchContent = el.target.value
    },
    cleanSuch () {
      this.suchContent = ''
      document.getElementById('suchContentInput').value = ''
    },
    changeLanguage (lang) {
      i18n.locale = lang
    }
  }
}
</script>

<style lang="sass">
$colorMain: #4A90E2
$colorGreen: #51E4C3
$colorSubFontColor: #4B4B4B

*
  font-size: 20px
  font-family: '微軟正黑體','Arial'
html,body
  padding: 0
  margin: 0
  height: 100vh
  width: 100vw
ul
  padding: 0
  margin: 0
li
  list-style: none
.contaner
  height: 100vh
  display: flex
  .userWrapper
    box-shadow: 4px 3px 5px -2px #CFCFCF
    width: 30%
    height: 100%
    overflow: scroll
    .tltle
      display: block
      padding: 10px 15px
      color: $colorSubFontColor
    .user
      border-top: solid 1px $colorGreen
      padding: 10px 15px
      display: flex
      &:last-child
        border-bottom: solid 1px $colorGreen
      .userImg
        height: 80px
        width: 80px
        border: solid 1px $colorGreen
        border-radius: 100%
        margin-right: 15px
      .userInfo
        .userNane,.userMessage
          display: block
        .userNane
          font-size: 22px
          font-weight: bold
          margin-bottom: 10px
          margin-top: 5px
        .userMessage
          color: $colorSubFontColor
  .MessageWrapper
    width: 70%
    position: relative
    .imgBox
      position: absolute
      top: 50%
      left: 50%
      transform: translate(-50%,-50%)
      img
        width: 300px
      p
        color: $colorMain
        text-align: center
        font-size: 30px
    .header
      background-color:  $colorMain
      display: flex
      justify-content: space-between
      height: 80px
      .navBar
        display: flex
        justify-content: space-between
        color: #fff
        width: calc(100% - 186px)
        padding: 15px
        box-sizing: border-box
        .title
          line-height: 60px
          color: #fff
          font-size: 30px
        .language
          display: flex
          align-items: center
          .langBtn
            margin-right: 10px
            padding: 5px 20px
            border-radius: 20px
            height: fit-content
            cursor: pointer
            border: solid 1px #fff
            &.active
              color: $colorMain
              background-color: #fff
      .loginUser
        display: flex
        padding: 10px
        border-left: solid 1px #fff
        .userImg
          height: 60px
          width: 60px
          border-radius: 100%
          margin-right: 15px
          background-color: #fff
        .userName
          line-height: 60px
          color: #fff
          font-size: 30px
    .welcomeWrapper
      width: 70%
    .message
      height: calc( 100% - 80px )
      .userBar
        height: 60px
        display: flex
        align-items: center
        justify-content: space-between
        padding: 10px 15px
        box-sizing: border-box
        box-shadow: 4px 3px 5px -2px #CFCFCF
        .userInfo
          display: flex
          align-items: center
          .userImg
            border: solid 1px $colorGreen
            height: 40px
            width: 40px
            border-radius: 100%
            margin-right: 10px
          .userName
            font-weight: bold
        .option
          .such,.note
            display: inline-block
            margin-right: 10px
            height: 37px
            width: 37px
            position: relative
            cursor: pointer
            img
              height: 30px
              width: 30px
              position: absolute
              top: 50%
              left: 50%
              transform: translate(-50%,-50%)
            &.active
              border: solid 2px #ddd
              border-radius: 100%
              box-sizing: border-box
      .messageContent
        height: calc( 100% - 120px )
        position: relative
        .msgWrapper
          display: flex
          flex-direction: column
          justify-content: flex-end
          height: 100%
          padding: 10px
          box-sizing: border-box
          li
            text-align: right
            margin-bottom: 30px
            .msg
              background-color: $colorMain
              color: #fff
              padding: 10px 15px
              box-sizing: border-box
              border-radius: 20px
              .highlight
                background-color: #F8E71C
                color: #000
        .suchWrapper
          padding: 5px
          position: relative
          border-bottom: solid 1px $colorGreen
          position: absolute
          width: 100%
          box-sizing: border-box
          input
            height: 100%
            width: 100%
            padding: 10px 50px 10px 15px
            box-sizing: border-box
            border: none
          span
            position: absolute
            top: 15px
            right: 35px
          .closeBtn
            position: absolute
            height: 20px
            width: 20px
            top: 19px
            right: 10px
            background-size: cover
            background-image: url('../assets/ic_close1.png')
            cursor: pointer
        .noteWrapper
          position: absolute
          padding: 10px
          right: 10px
          top: 10px
          border: solid 1px #ddd
          border-radius: 10px
          //display: none
          &::after
            content: ''
            width: 0
            height: 0
            border-style: solid
            border-width: 0 12.5px 25px 12.5px
            border-color: transparent transparent #ddd transparent
            position: absolute
            top: -25px
            right: 23px
          &::before
            content: ''
            width: 0
            height: 0
            border-style: solid
            border-width: 0 10.5px 21px 10.5px
            border-color: transparent transparent #fff transparent
            position: absolute
            top: -21px
            right: 25px
            z-index: 1
          textarea
            padding: 10px
            border: solid 1px $colorMain
          .sandBtn
            background-color: $colorMain
            color: #fff
            text-align: center
            padding: 5px
            margin-bottom: 15px
          ul
            padding-top: 15px
            border-top: solid 1px $colorGreen
            li
              border: solid 1px $colorGreen
              padding: 5px
              margin-bottom: 10px
              position: relative
              .delBtn
                height: 15px
                width: 15px
                background-size: cover
                background-image: url('../assets/ic_close2.png')
                position: absolute
                top: 10px
                right: 10px
                cursor: pointer
              .dateTime
                color: $colorMain
      .MessageInput
        height: 60px
        position: relative
        input
          height: 100%
          width: 100%
          padding: 10px 50px 10px 15px
          box-sizing: border-box
        .sendBtn
          height: 40px
          width: 40px
          background-image: url('../assets/ic_sent.png')
          position: absolute
          top: 10px
          right:10px
          background-size: cover
          cursor: pointer
</style>
