<template>
  <div>
    <a class="beer-wrapper">
      <img :src="beer.image_url"/>
      <h3>{{beer.name}}</h3>
      <p>{{beer.tagline}}</p>
    </a>
  </div>
</template>

<script>
export default {
  name: 'BeerTile',
  props: {
    beer: {
      type: Object
    },
  }
}
</script>

<style lang="scss" scroped>
.beer-wrapper {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-content: space-between;
  align-items: center;
  height: 300px;
  width: 300px;
  background-color: white;
  border-radius: 10px;
  padding: 1rem;
  overflow: hidden;
  text-align: center;
  transition: 300ms;
  box-shadow: 1px 1px 2px 1px rgba(160, 158, 158,0.6),
              -1px -1px 2px 1px rgba(160, 158, 158,0.6);
  &:hover,
  &:focus {
    box-shadow: 2px 2px 5px 4px rgba(160, 158, 158, 0.4),
               -2px -2px 5px 4px rgba(160, 158, 158,0.4);
    transform: scale(1.05);
  }
  h3 {
    text-overflow: ellipsis;
    margin: 1rem 0 0.5rem;
  }
  img {
    max-width:65%;
    max-height:65%;
  }
  p {
    margin: 0;
  }
}
</style>
