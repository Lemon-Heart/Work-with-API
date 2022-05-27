<template>
  <aside class="sidebar">
    <div class="sidebar__user">
      <span>User ID: {{ $cookies.get('id') }}</span>
    </div>
    <div class="sidebar__balance">
      <div v-for="(item, i) in balance" :key="item.id">
        <h3>{{ balanceTitle[i] }}</h3>
        <span>{{ item.attributes.available }} {{ item.attributes.currency }}</span>
      </div>
    </div>
    <div class="sidebar__logout">
      <button class="btn" @click="$emit('logout')">
        Log out
      </button>
    </div>
  </aside>
</template>

<script>
export default {
  name: 'TheSidebar',
  props: {
    balance: {
      type: Array,
      required: true
    },
    balanceTitle: {
      type: Array,
      required: true
    }
  }
}
</script>
