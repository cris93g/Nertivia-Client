<template>
  <div class="pending-friends" v-if="friends && friends.length">
    <div class="tab" @click="expanded = !expanded">
      <Tab :expanded="expanded" tabname="Pending requests" />
    </div>
    <transition name="list">
      <div v-if="expanded" class="list">
        <PendingTemplate
          v-for="(friend, key) of friends"
          :key="key"
          :unique-i-d="friend.recipient.uniqueID"
          :status="friend.status"
          :username="friend.recipient.username"
          :tag="friend.recipient.tag"
        />
      </div>
    </transition>
  </div>
</template>

<script>
import Tab from "./Tab.vue";
import PendingTemplate from "./PendingTemplate.vue";
export default {
  components: {
    Tab,
    PendingTemplate
  },
  data() {
    return {
      expanded: true
    };
  },
  computed: {
    friends() {
      const allFriend = this.$store.getters.user.friends;
      const members = this.$store.getters["members/members"];
      const result = Object.keys(allFriend).map(function(key) {
        allFriend[key].recipient = members[allFriend[key].uniqueID];
        return allFriend[key];
      });
      return result.filter(friend => friend.status < 2);
    }
  }
};
</script>
<style scoped>
.list-enter-active,
.list-leave-active {
  transition: 0.3s;
}
.list-enter, .list-leave-to /* .fade-leave-active below version 2.1.8 */ {
  transform: translateY(-20px);
  opacity: 0;
}

.pending-friends {
  background-color: rgba(0, 0, 0, 0);
  user-select: none;
  padding-bottom: 3px;
  transition: 0.3s;
}
.tab {
  transition: 0.3s;
}
.tab:hover {
  background-color: rgba(0, 0, 0, 0.123);
}
</style>
