<template>
  <div id="app">
    <vue-headful title="Nertivia" description="Nertivia Chat Client" />
    <div ref="backgroundImage" class="background" />
    <div class="content">
      <transition name="fall-down" appear>
        <div class="header">
          <div class="logo" />
          <div class="name">Nertivia</div>
          <div class="links">
            <div v-if="!loggedIn" class="link" @click="signupPage">Sign up</div>
            <div v-if="!loggedIn" class="link" @click="loginPage">Login</div>
            <spinner
              v-if="loggedIn && !user"
              class="spinner-profile"
              :size="50"
            />
            <profile-picture
              v-if="loggedIn && user"
              class="avatar"
              :hover="true"
              :url="avatarDomain + user.avatar"
              :admin="user.admin"
              size="40px"
              emote-size="17px"
              @click.native="showProfilePopout = !showProfilePopout"
            />
            <transition name="fall-down-fast">
              <profile-popout
                v-if="user && loggedIn && showProfilePopout"
                v-click-outside="closeProfilePopout"
                :user="user"
                @logout="logOut"
              />
            </transition>
          </div>
        </div>
      </transition>
      <transition name="side-in" appear>
        <div class="inner-content">
          <div class="title">
            The best chat client that won't restrict you from important and fun
            features.
          </div>
          <img class="graphic" src="@/assets/graphics/HomeGraphics.png" />
          <div class="buttons">
            <div class="button" @click="openApp">Open In Browser</div>
            <div class="button download" @click="showDownloadsPopout = true">
              Download App
            </div>
          </div>
          <div class="features-list">
            <div class="title">Things you can do in Nertivia</div>
            <div class="list">
              <div class="feature">
                <i class="material-icons">insert_drive_file</i>
                <div class="description">
                  Link Nertivia with Google Drive to upload large files.
                </div>
              </div>
              <div class="feature">
                <i class="material-icons">face</i>
                <div class="description">
                  Free custom gif emojis and profile picture.
                </div>
              </div>
              <div class="feature">
                <i class="material-icons">storage</i>
                <div class="description">
                  Make your own servers with channels.
                </div>
              </div>
            </div>
          </div>
        </div>
      </transition>
    </div>
    <div class="popouts">
      <transition name="fade">
        <download-app-popout
          v-if="showDownloadsPopout"
          @close="showDownloadsPopout = false"
        />
      </transition>
    </div>
  </div>
</template>

<script>
import Spinner from "@/components/Spinner.vue";
import ProfilePicture from "@/components/ProfilePictureTemplate.vue";
import ProfilePopout from "@/components/homePage/ProfilePopout.vue";
import DownloadAppPopout from "@/components/homePage/DownloadAppPopout.vue";
import AuthenticationService from "@/services/AuthenticationService.js";
import config from "@/config.js";

export default {
  components: { Spinner, ProfilePicture, ProfilePopout, DownloadAppPopout },
  data() {
    return {
      showDownloadsPopout: false,
      showProfilePopout: false,
      loggedIn: localStorage.getItem("hauthid") || null,
      user: null,
      avatarDomain: config.domain + "/avatars/"
    };
  },
  async mounted() {
    if (this.loggedIn) this.getUser();
  },
  methods: {
    closeProfilePopout(event) {
      if (!event.target.closest(".avatar")) this.showProfilePopout = false;
    },
    logOut() {
      this.$store.dispatch("logout");
      this.loggedIn = null;
    },
    loginPage() {
      window.location.href = "/login";
    },
    signupPage() {
      window.location.href = "/register";
    },
    openApp() {
      window.location.href = "/app";
    },
    async getUser() {
      const { ok, error, result } = await AuthenticationService.user();
      if (!ok) {
        // connection error
        if (error.response === undefined) {
          setTimeout(() => {
            this.getUser();
          }, 5000);
          return;
        } else {
          localStorage.removeItem("hauthid");
          this.loggedIn = null;
        }
      } else {
        this.user = result.data.user;
      }
    }
  }
};
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}

.fall-down-enter-active {
  opacity: 0;
  animation: fall-down 0.5s;
  animation-delay: 0.3s;
}

.fall-down-fast-enter-active {
  opacity: 0;
  animation: bounce-in 0.2s;
}
.fall-down-fast-leave-active {
  opacity: 0;
  animation: bounce-in 0.2s reverse;
}

@keyframes fall-down {
  0% {
    transform: translateY(-20px);
    opacity: 0;
  }
  50% {
    transform: translateY(10px);
    opacity: 1;
  }
  100% {
    transform: translateY(0);
    opacity: 1;
  }
}

@keyframes bounce-in {
  0% {
    transform: translateY(-20px);
    opacity: 0;
  }
  50% {
    transform: translateY(10px);
    opacity: 1;
  }
  100% {
    transform: translateY(0);
    opacity: 1;
  }
}

.side-in-enter-active {
  opacity: 0;
  animation: side-in 0.5s;
  animation-delay: 0.5s;
}

@keyframes side-in {
  0% {
    transform: translateX(-20px);
    opacity: 0;
  }
  50% {
    transform: translateX(10px);
    opacity: 1;
  }
  100% {
    transform: translateX(0);
    opacity: 1;
  }
}

html,
body {
  height: 100%;
}
#app {
  display: flex;
  flex-direction: column;
  height: 100%;
}
.header {
  display: flex;
  height: 70px;
  flex-shrink: 0;
  border: 10px;
  position: relative;
  background: #072834;
}
.logo {
  background: url("../assets/logo.png");
  background-size: 100%;
  height: 50px;
  width: 50px;
  margin-top: auto;
  margin-bottom: auto;
  margin-left: 20px;
  background-repeat: no-repeat;
}
.name {
  margin-top: auto;
  margin-bottom: auto;
  font-size: 20px;
  margin-left: 10px;
  color: white;
}

.background {
  z-index: -1;
  position: fixed;
  width: 100%;
  height: 100%;
  transition: 0.5s;
  background: linear-gradient(#0b4155, #01677e);
}
.content {
  position: fixed;
  width: 100%;
  height: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  overflow: auto;
  overflow-x: hidden;
}

.inner-content {
  display: flex;
  flex-direction: column;
  width: 100%;
  height: 100%;
  flex: 1;
  margin-top: 40px;
}
.graphic {
  width: 700px;
  height: auto;
  margin: auto;
  margin-top: 10px;
  margin-bottom: 0;
  user-select: none;
  flex-shrink: 0;
  border: solid 2px #0f272a;
  border-radius: 4px;
}
.title {
  font-size: 25px;
  color: white;
  text-align: center;
  padding: 10px;
  flex-shrink: 0;
}
.buttons {
  display: flex;
  justify-content: center;
  margin-top: 30px;
  flex-shrink: 0;
}
.button {
  padding: 15px;
  background: rgba(24, 132, 255, 0.733);
  color: white;
  user-select: none;
  transition: 0.3s;
  margin: 10px;
  cursor: pointer;
  border-radius: 4px;
}

.button:hover {
  background: rgb(24, 132, 255);
}

.button.download {
  background: rgba(0, 223, 67, 0.733);
}

.button.download:hover {
  background: rgba(0, 223, 67, 0.904);
}

.features-list {
  margin-top: 20px;
}
.features-list .list {
  display: flex;
  justify-content: center;
}
.feature {
  background: #024352;
  color: white;
  margin: 10px;
  padding: 2px;
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  justify-content: center;
  height: 200px;
  width: 200px;
  flex-shrink: 0;
  border-radius: 4px;
  transition: 0.3s;
}
.feature .description {
  width: 150px;
  margin-top: 15px;
}
.feature .material-icons {
  font-size: 60px;
}
.links {
  color: white;
  margin: auto;
  margin-right: 20px;
  display: flex;
}
.link {
  padding: 10px;
  background: #25424d;
  user-select: none;
  margin-left: 5px;
  transition: 0.3s;
  cursor: pointer;
  border-radius: 4px;
}
.link:hover {
  background: rgba(255, 255, 255, 0.26);
}
.warn {
  color: red;
}

@media (max-width: 1000px) {
  .graphic {
    width: calc(100% - 30px);
  }
}

@media (max-width: 662px) {
  .feature {
    margin: 5px;
    padding: 0px;
    height: 150px;
    width: 150px;
  }
}

@media (max-width: 334px) {
  .buttons {
    flex-direction: column;
  }
  .buttons .button {
    text-align: center;
  }
}

@media (max-width: 465px) {
  .features-list .list {
    flex-direction: column;
  }
  .feature {
    margin: 5px;
    padding: 0px;
    height: 150px;
    width: auto;
    font-size: 15px;
  }
  .feature .description {
    margin-top: 15px;
    width: 100%;
  }
}
</style>

<style>
body {
  background: rgb(46, 37, 49);
}
</style>
