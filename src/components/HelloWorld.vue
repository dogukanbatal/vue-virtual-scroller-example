<script>
import ItemCard from "@/components/Item";
import { generateMessage } from "../data";
import { DynamicScroller, DynamicScrollerItem } from "vue-virtual-scroller";
import "vue-virtual-scroller/dist/vue-virtual-scroller.css";

let id = 0;
const messages = [];
for (let i = 0; i < 50; i++) {
  messages.push(generateMessage());
}

export default {
  components: { ItemCard, DynamicScroller, DynamicScrollerItem },

  data() {
    return {
      items: [],
      search: "",
      streaming: false,
    };
  },
  computed: {
    filteredItems() {
      const { search, items } = this;
      if (!search) return items;
      const lowerCaseSearch = search.toLowerCase();
      return items.filter((i) =>
        i.message.toLowerCase().includes(lowerCaseSearch)
      );
    },
  },
  unmounted() {
    this.stopStream();
  },
  methods: {
    changeMessage(message) {
      Object.assign(message, generateMessage());
    },
    addMessage() {
      for (let i = 0; i < 10; i++) {
        this.items.push({
          id: id++,
          ...messages[id % 50],
        });
      }
      this.scrollToBottom();
      if (this.streaming) {
        requestAnimationFrame(this.addMessage);
      }
    },
    scrollToBottom() {
      this.$refs.scroller.scrollToBottom();
    },
    startStream() {
      if (this.streaming) return;
      this.streaming = true;
      this.addMessage();
    },
    stopStream() {
      this.streaming = false;
    },
  },
};
</script>

<template>
  <div class="chat-demo">
    <div class="toolbar">
      <button v-if="!streaming" @click="startStream()">Start stream</button>
      <button v-else @click="stopStream()">Stop stream</button>

      <input v-model="search" placeholder="Filter..." />
    </div>

    <DynamicScroller
      ref="scroller"
      :items="filteredItems"
      :min-item-size="54"
      class="scroller"
    >
      <template #before>
        <div class="notice">The message heights are unknown.</div>
      </template>

      <template #default="{ item, index, active }">
        <DynamicScrollerItem
          :item="item"
          :active="active"
          :size-dependencies="[item.message]"
          :data-index="index"
          :data-active="active"
          :title="`Click to change message ${index}`"
          class="message"
          @click="changeMessage(item)"
        >
          <ItemCard :item="item" />
        </DynamicScrollerItem>
      </template>
    </DynamicScroller>
  </div>
</template>

<style scoped>
.chat-demo {
  overflow: hidden;
  flex: auto 1 1;
  display: flex;
  flex-direction: column;
}
.scroller {
  flex: auto 1 1;
}
.notice {
  padding: 24px;
  font-size: 20px;
  color: #999;
}
.message {
  display: flex;
  min-height: 32px;
  padding: 12px;
  box-sizing: border-box;
}
.avatar {
  flex: auto 0 0;
  width: 32px;
  height: 32px;
  border-radius: 50%;
  margin-right: 12px;
}
.avatar .image {
  max-width: 100%;
  max-height: 100%;
  border-radius: 50%;
}
.index,
.text {
  flex: 1;
}
.text {
  max-width: 400px;
}
.index {
  opacity: 0.5;
}
.index span {
  display: inline-block;
  width: 160px;
  text-align: right;
}
</style>
