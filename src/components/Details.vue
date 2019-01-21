<template>
  <transition
    name="slide"
  >
    <div class="details-overlay" @click.stop="close">
      <button class="return" @click="close">BACK</button>
      <beer-link :to="index+1" class="next" ref="next">
        <svg v-if="!error" x="0px" y="0px" viewBox="0 0 232.153 232.153" class="arrow" xml:space="preserve">
          <g id="Play">
            <path fill="orange" d="M203.791,99.628L49.307,2.294c-4.567-2.719-10.238-2.266-14.521-2.266
              c-17.132,0-17.056,13.227-17.056,16.578v198.94c0,2.833-0.075,16.579,17.056,16.579c4.283,0,9.955,0.451,14.521-2.267
              l154.483-97.333c12.68-7.545,10.489-16.449,10.489-16.449S216.471,107.172,203.791,99.628z"/>
          </g>
        </svg>
      </beer-link>
      <beer-link :to="index-1" class="prev" ref="prev" v-if="index-1>=0">
        <svg v-if="!error" x="0px" y="0px" viewBox="0 0 232.153 232.153" class="arrow" xml:space="preserve">
          <g id="Play">
            <path fill="orange" d="M203.791,99.628L49.307,2.294c-4.567-2.719-10.238-2.266-14.521-2.266
              c-17.132,0-17.056,13.227-17.056,16.578v198.94c0,2.833-0.075,16.579,17.056,16.579c4.283,0,9.955,0.451,14.521-2.267
              l154.483-97.333c12.68-7.545,10.489-16.449,10.489-16.449S216.471,107.172,203.791,99.628z"/>
          </g>
        </svg>
      </beer-link>

      <div @click.stop class="card">
        <div v-if="error===true" class="error-container">
          <svg class="
          " x="0px" y="0px" width="512px" height="512px" viewBox="0 0 512 512" enable-background="new 0 0 512 512" xml:space="preserve" >
            <path fill="orange" d="M256,64c105.875,0,192,86.125,192,192s-86.125,192-192,192S64,361.875,64,256S150.125,64,256,64 M256,32  C132.281,32,32,132.281,32,256s100.281,224,224,224s224-100.281,224-224S379.719,32,256,32L256,32z M192,144  c-17.688,0-32,21.5-32,48c0,26.531,14.313,48,32,48s32-21.469,32-48C224,165.5,209.688,144,192,144z M320,144  c-17.688,0-32,21.5-32,48c0,26.531,14.313,48,32,48s32-21.469,32-48C352,165.5,337.688,144,320,144z M393.594,364.813  c7.063-5.313,8.5-15.344,3.219-22.406C363.719,298.313,311.094,272,256,272s-107.734,26.313-140.797,70.406  c-5.313,7.063-3.875,17.094,3.188,22.406c7.078,5.281,17.094,3.844,22.406-3.219C167.859,325.531,210.922,304,256,304  c45.063,0,88.156,21.531,115.188,57.594C374.344,365.781,379.156,368,384,368C387.344,368,390.719,366.969,393.594,364.813z"/>
          </svg>
          <h2 class="error-message">SORRY, WE DON'T HAVE THAT BEER</h2>
        </div>
        <div v-else-if="mainBeer">
          <main>
            <div class="image">
              <img :src="mainBeer.image_url"/>
            </div>
            <div class="text-content">
              <h3>{{mainBeer.name}}</h3>
              <h4>{{mainBeer.tagline}}</h4>
              <div class="stats">
                <strong>IBU:</strong> {{mainBeer.ibu || "--"}}
                <strong>ABV:</strong> {{mainBeer.abv || "--"}}
                <strong>EBC:</strong> {{mainBeer.ebc || "--"}}
              </div>
              <p>{{mainBeer.description}}</p>
              <p>{{mainBeer.brewers_tips}}</p>
              <div class="food"><p>It goes well with:</p>
                <ul>
                  <li v-for="pair in mainBeer.food_pairing" :key="pair">{{pair}}</li>
                </ul>
              </div>
            </div>
          </main>
        <p class="you-might">You might as well like these:</p>
        <div class="suggestions">
          <div v-if="ibuBeer" class="box">
              <span class="tag">similar IBU</span>
            <beer-link :to="`${ibuBeer.id-1}`">
              <img :src="ibuBeer.image_url"/><br/>
              <span>{{ibuBeer.name}}</span>
            </beer-link>
          </div>
          <div v-if="abvBeer" class="box">
              <span class="tag">similar ABV</span>
            <beer-link :to="`${abvBeer.id-1}`">
              <img :src="abvBeer.image_url"/><br/>
              <span>{{abvBeer.name}}</span>
            </beer-link>
          </div>
          <div v-if="ebcBeer" class="box">
              <span class="tag">similar EBC</span>
            <beer-link :to="`${ebcBeer.id-1}`">
              <img :src="ebcBeer.image_url"/><br/>
              <span>{{ebcBeer.name}}</span>
            </beer-link>
          </div>
        </div>
      </div>
      </div>
    </div>
  </transition>
</template>

<script>
import BeerLink from './BeerLink.vue';

export default {
  name: 'Details',
  props: {
    beers: {
      type: Array,
    },
    index: {
      type: [Number, String]
    },
    error: {
      type: Boolean,
    }
  },
  components: {
    BeerLink
  },
  data() {
    return {
      ibuBeer: {
      },
      abvBeer: {
      },
      ebcBeer: {
      },
    }
  },
  created() {
    window.addEventListener('keyup', this.close)
  },
  destroyed() {
    window.removeEventListener('keyup', this.close)
  },
  mounted() {
    this.updateSuggestions()
  },
  methods: {
    close(event) {
      let newIndex="";
      if ( event.target.className==="return" || event.target.className==="details-overlay" ||  event.keyCode===27) {
        this.$emit('closed', this);
      }
      else if (( event.target.className==="arrow-right" || event.keyCode===39) && (!this.error)) {
        if (newIndex > this.beers.length) {
          newIndex = String(this.beers.length-1);
          return;
        }
        newIndex = String(this.index+1);
      }
      else if (( event.target.className==="arrow-left" || event.keyCode===37) && (!this.error)) {
        if (newIndex > 0) {
          newIndex = String(this.beers.length-1);
          return;
        }
        newIndex = String(this.index-1);
      }
      this.$emit('open', newIndex)
    },

    updateSuggestions() {
      if (this.beers) {
        this.ibuBeer=this.similar('ibu')
        this.abvBeer=this.similar('abv')
        this.ebcBeer=this.similar('ebc')
      }
    },
    similar(type) {
      if (this.beers.length&&this.mainBeer) {
        // To find a beer that has a similar IBU
        // (to be able to pass it in as an object and ultimately
        // have it rendered as a link)
        // I decided to:
        // 1. Sort the beers array using .sort()
        // (the .slice() part is used here as a cheap way of
        // copying the array - otherwise, the whole app gets
        // into an infinite render & sort loop)
        const sorted = this.beers.slice().sort(function (a, b) {
          return a[type] - b[type]
        })
        // 2. Once we have this sorted out (hehe), let's
        // find a position of our current beer's index in
        // this new order
        const index = sorted.findIndex(x=>x.id===this.mainBeer.id)
        // 3. Finally, let's have our function return a beer
        // that is closest the the given one, with a small guard
        // in case it'd go over the array's ledge.
        let near;
        if (index === this.beers.length) {
          near = index-1
        }
        else {
          near = index+1
        }
        return sorted[near];
      } else {
        return {}
      }
    },
    handleBoxClick(number) {
      this.$emit('open', number)
    }
  },
  computed: {
    mainBeer() {
      return this.beers[this.index]
    },
    calculateNext() {
      return this.index+1
    },
  },
  watch: {
    beers() {
      if (this.mainBeer) {
        this.updateSuggestions();
      }
    },
    $route() {
      this.updateSuggestions();
    }
  }
}
</script>

<style lang="scss" scoped>
body {
  overflow: hidden;
}
.error {
  position: absolute;
}
/* Reset `button` and button-style `input` default styles */
input[type="submit"],
input[type="reset"],
input[type="button"],
button {
    background: none;
    border: 0;
    color: inherit;
    /* cursor: default; */
    font: inherit;
    line-height: normal;
    overflow: visible;
    padding: 0;
    -webkit-appearance: button; /* for input */
    -webkit-user-select: none; /* for button */
       -moz-user-select: none;
        -ms-user-select: none;
}
input::-moz-focus-inner,
button::-moz-focus-inner {
    border: 0;
    padding: 0;
}

.details-overlay {
  position: fixed;
  top: 0;
  left: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: rgba(100,100,100,0.9);
  min-height: 100vh;
  min-width: 100vw;
  z-index: 1;

  .return {
    position:fixed;
    right: 4rem;
    top: 1.2rem;
    height: 70px;
    width: 70px;
    border-radius: 50%;
    background-color: orange;
    color: white;
    font-weight: bold;
    transform: rotate(20deg);
    transition: 500ms;

    &:hover {
      transform: scale(1.2);
      transition: 500ms;
    }
  }

  .arrow {
    enable-background: new 0 0 232.153 232.153;
    z-index:2;
  }

  .next,
  .prev {
    width:100px;
    position: fixed;
    top: 50vh;
    transition: 500ms;
    path {
      fill-rule:evenodd;
      clip-rule:evenodd;
    }
  }
  .next {
    right:2px;
    &:hover {
      transform: scale(1.2);
      transition: 500ms;
    }
  }
  .prev {
    left: 2px;
    transform: rotate(180deg);
    &:hover {
        transform: scale(1.2);
    transform: rotate(180deg);
        transition: 500ms;
      }
    }

  .card {
    flex-wrap: wrap;
    max-width: 80vw;
    min-height: 90vh;
    background-color: white;
    border-radius: 1rem;
    padding: 2rem;
    display: flex;
    box-shadow: 2px 2px 200px 4px rgba(71, 70, 70, 0.95),
               -2px -2px 200px 4px rgba(71,70,70,0.95);
    max-height: 100vh;
    overflow-y: auto;
    overflow-x: hidden;
    letter-spacing: 0.1rem;

    &.error-container {
      width:512px;
      height:600px;
      &.error-message {
        color:orange;
        margin-top:600px;
        font-size:4rem;
        text-align:center;
      }
    }
    main {
      display: flex;
      flex-direction: row;
      .image {
        display: flex;
        flex-direction:column;
        max-height: 50vh;
        max-width: 30%;

        img {
        height: 100%;
        align-self: center;
        }
      }
      .text-content {
        display: flex;
        flex-direction: column;
        margin-left: 2.2rem;

        h3,h4 {
        margin: 0;
          margin-bottom: 0.5rem
        }
        h3 {
          font-size: 2rem;
          letter-spacing: 0.15rem;
        }
        h4 {
          font-size: 1.1rem;
          letter-spacing: 0.1rem;
          font-weight: lighter;
        }
        p {
          margin-bottom: 0;
          line-height: 1.3rem;
        }
        .stats {
          display: flex;
          flex-wrap: wrap;
          justify-content: space-between;
          font-size: 1.3rem;
          letter-spacing: 0.1rem;
          margin: 1rem;
        }
        .food {
          ul {
            margin-bottom: 0;
          }
        }
      }
    }
    .you-might {
      font-size: 1.8rem;
    }
    .suggestions {
      max-height: 35%;
      display: flex;
      flex-direction: row;
      justify-content: space-around;
      width: 100%;
      .box {
        text-align:center;
        max-height: 15vh;
        width: 33.3%;
        transition: 500ms;
        position: relative;
          .tag {
            position: absolute;
            font-size: 0.7rem;
            top: 0rem;
            right: 1rem;
            background-color: orange;
            padding: 2px 6px;
            border-radius: 8px;
            border: 3px dotted grey;
            color: white;
          }
        a {
          color: inherit;
          text-decoration: none;
          font-size: 1.2rem;
          letter-spacing:0.1rem;
          img {
            height: 100%;
          }
          span {
            width: 100%
          }
        &:hover {
          transform: scale(1.2);
          transition: 500ms;
        }
        }
      }
    }
    &>p {
      margin:0;
      margin-bottom:0;
      margin-block-start:0;
      margin-block-end:0;
    }
  }
}
.slide-enter-active, .slide-leave-active {
  transition: 0.5s;
}
.slide-enter, .slide-leave-to {
  transform: translateY(100vh) rotateY(90deg);
  opacity:0;
}

</style>
