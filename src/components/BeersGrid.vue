<template>
  <div class="grid">
        <router-link
          :to="`${beer.id-1}`"
          :key="beer.id"
          v-for="beer in beers"
          class="tile-wrapper"
          :beer="beer"
        >
        <beer-tile
            :beer="beer"
          >
        </beer-tile>
      </router-link>

    <div class="no-more" v-if="enough">
        <button @click="backToTop">
          <p>no more :(</p>
          back to the top
        </button>
    </div>
  </div>
</template>

<script>
import BeerTile from './BeerTile';

export default {
  name: 'BeersGrid',
  components: {
    BeerTile
  },
  props: {
    beers: {
      type: Array,
      default: ()=>[]
    },
    enough: {
      type: Boolean,
      default: false
    }
  },
  methods: {
    backToTop() {
      document.body.scrollTop = 0; // For Safari
      document.documentElement.scrollTop = 0; // for the rest
    }
  }
}
</script>

<style scoped lang="scss">
.grid {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: center;
  align-content: center;
  overflow-x: hidden;
  z-index:2;
  .tile-wrapper {
    margin: 1rem;
    color: inherit;
    text-decoration: none;
  }
  .no-more {
    display: block;
    width: 100%;
    text-align: center;
    font-size: 5rem;
    color: orange;
    p {
      margin:0;
    }
    button {
      min-width: 40%;
      font-size:4rem;
      color: orange;
      margin-bottom: 1rem;

    }
  }
}
</style>
