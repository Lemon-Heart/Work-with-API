<template>
  <div class="games__item" @click="$emit('play')">
    <div class="games__fav" :class="{'active': isFav}" @click.stop="">❤</div>
    <div class="games__image" :style="{backgroundImage: 'url(' + image + ')'}" />
    <span>{{ title }}</span>
  </div>
</template>

<script>
export default {
  name: 'AppGamesCart',
  props: {
    image: {
      type: String,
      required: true
    },
    title: {
      type: String,
      required: true
    },
    isFav: {
      type: Boolean,
      default: false
    }
  }
}
</script>
