<template>
  <div class="drop-down-menu" v-click-outside="outsideClick">
    <div class="item" @click="createInvite">Manage Invites</div>
    <div class="item" v-if="checkServerCreator" @click="showSettings">
      Server Settings
    </div>
    <div class="item warn" v-if="!checkServerCreator" @click="leaveServer">
      Leave Server
    </div>
    <div class="item" @click="copyServerID">
      Copy Server ID
    </div>
  </div>
</template>

<script>
import ServerService from "../../../../services/ServerService";
export default {
  data() {
    return {};
  },
  methods: {
    closeMenu() {
      this.$store.dispatch("setAllPopout", {
        show: false,
        type: null
      });
    },
    outsideClick(event) {
      if (event.target.classList.contains("options-button")) return;
      this.closeMenu();
    },
    setPosition() {
      const y = this.contextDetails.y;
      const x = this.contextDetails.x;

      this.$el.style.top = y + "px";
      this.$el.style.left = x + "px";
    },
    showSettings() {
      this.closeMenu();
      this.$store.dispatch("setServerSettings", {
        serverID: this.contextDetails.serverID
      });
    },
    createInvite() {
      this.closeMenu();
      this.$store.dispatch("setPopoutVisibility", {
        name: "showServerInviteMenu",
        visibility: true
      });
    },
    async leaveServer() {
      this.closeMenu();
      await ServerService.leaveServer(this.contextDetails.serverID);
    },
    copyServerID() {
      this.closeMenu();
      this.$clipboard(this.contextDetails.serverID);
    }
  },
  mounted() {
    this.setPosition();
  },
  watch: {
    contextDetails() {
      this.setPosition();
    }
  },
  computed: {
    contextDetails() {
      return this.$store.getters.popouts.allPopout;
    },
    user() {
      return this.$store.getters.user;
    },
    checkServerCreator() {
      return this.contextDetails.creatorUniqueID === this.user.uniqueID;
    }
  }
};
</script>

<style lang="scss" scoped>
.drop-down-menu {
  position: absolute;
  top: 0;
  left: 0;
  background: rgba(0, 0, 0, 0.7);
  backdrop-filter: blur(5px);
  z-index: 99999;
  user-select: none;
  color: white;
}

.item {
  padding: 10px;
  transition: 0.3s;
  font-size: 13px;
  cursor: pointer;
  &:hover {
    background: rgba(46, 46, 46, 0.651);
  }
  &.warn {
    color: rgb(255, 59, 59);
  }
}
</style>
