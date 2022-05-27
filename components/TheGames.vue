<template>
  <div class="games">
    <app-games-cart
      v-for="item in games"
      :key="item.id"
      :image="item.attributes.image"
      :is-fav="item.attributes['is-favorite']"
      :title="item.attributes.title"
      @play="$emit('play', item.id)"
    />
  </div>
</template>

<script>
export default {
  name: 'TheGames',
  props: {
    games: {
      type: Array,
      required: true
    }
  }
}
</script>
