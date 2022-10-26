<template>
  <div>
    <div class="avatar">
      <img :key="item.avatar" :src="item.avatar" alt="avatar" class="image" />
    </div>
    <div class="text">
      {{ item.message }}
    </div>
    <div class="index">
      <span>{{ item.id }} (id)</span>
      <span></span>
    </div>
  </div>
</template>
<script>
export default {
  name: "ItemCard",
  props: ["item"],
};
</script>
