<template>
  <div id="app">
    <div id="nav">
      <h1 class="logo">P i w a p p </h1>
    </div>
      <div class="home"
            v-infinite-scroll="fetchBeers"
            infinite-scroll-disabled="isLoading"
            infinite-scroll-distance="10">
        <beers-grid :beers="fetchedBeers"
                    @clicked="open"
                    :enough="allBearsRenderedAlready"/>
        <div class="loading" v-if="isLoading">
          <img src="@/assets/beer.png" class="spinning"/>
        </div>
      </div>
    <router-view  :beers="fetchedBeers"
                  :index="currentBeerIndex"
                  @open="open"
                  @closed="close"
                  :error="error"
                  :key="currentBeerIndex"
                  :enough="allBearsRenderedAlready"/>
  </div>
</template>

<script>
import BeersGrid from "@/components/BeersGrid";
import Details from "@/components/Details";

// it is a hack, I should not be able to know this:
const NUM_OF_BEERS = 234;
const NUM_PER_BATCH = 80;

export default {
  components: {
    BeersGrid,
    Details
  },
  name: 'Piwapp',
  data() {
    return {
      fetchedBeers: [],
      isLoading: false,
      pageIndex: 0,
      allBearsRenderedAlready: false,
      currentBeerIndex: null,
      intervalID:"",
      error: false
    }
  },
  mounted() {
    this.evaluatePlace();
  },
  methods: {
    // crucial: fetching mechanism
    // 1. fetch - but only if you do not exceed the number
    //    of available "pages" (see API: https://punkapi.com/documentation/v2)
    // 2. bump up the pageIndex data property, and fetch, then apply the returned
    //    JSON array to the array of beers already had. Tertiary on line 83
    //    is used for the UX in case we keep on fetching - we don't want the
    //    loading screen to be blinking on and off (more on that below)
    // 3. in case we got all the beers already, flip boolean, clear interval

    fetchBeers(bool=false) {
      if (this.pageIndex < NUM_OF_BEERS/NUM_PER_BATCH) {
        this.isLoading = true;
        this.pageIndex++;
        let self = this;
        fetch(self.url)
          .then(function(response) {
            return response.json();
          })
          .then(function(myJson) {
            self.fetchedBeers.push.apply(self.fetchedBeers, myJson);
            bool==false ? self.isLoading = false: false;
          });
      } else {
        this.allBearsRenderedAlready = true;
        clearInterval(this.intervalID)
      }
    },
    // crucial: managing internal state using url
    // 1. in case we are at the root path, nullify beer index
    //    and fetch, if you haven't already
    // 2. in case there is something else in the path, try to
    //    turn it into a number and slice out the slash. In case
    //    it actually IS and number, and there are some beers fetched and
    //    the said number does not exceed the possible number of beers, proceed.
    //    Else - return error.
    // 3. if the beer it is inquired for is fetched, display. If not, fetch beers
    //    as many times as is necessary for the beer to be included in the fetched
    //    batched of 80 beers.

    evaluatePlace() {
      document.body.classList = '';

      let n = this.$route.path;
        this.error = false;
      // start:
      if (n === "/") {
        this.currentBeerIndex = null
        this.fetchedBeers===[] ? this.fetchBeers() : false;
      } else {
        let newIndex = Number(n.slice(1))
        if (!isNaN(newIndex) && this.fetchedBeers.length && newIndex<=NUM_OF_BEERS-1) {
          if (newIndex >= 0 && newIndex < this.fetchedBeers.length) {
            this.open(newIndex);
            return;
          } else if (newIndex >= this.fetchedBeers.length) {
            this.keepOnFetching(this.calculateBatch(newIndex));
            this.open(newIndex);
            return;
          }
        }  else {
            this.error=true
            return
          }
      }
    },
    // helper function
    calculateBatch(n) {
      var i = 1;
      while (n >  i * NUM_PER_BATCH) {
        i++
      }
      return i;
    },
    // this is how we
    keepOnFetching(n) {
      // the calculateBatch function tells us how many times we need to
      // fetch a batch of 80 to get the desired beer. Since the API allows
      // for requests for (only) 80 beers and (only) every second, we need
      // to set intervals every second to fetch a batch of 80 specified
      // number of times.
      var i = 1;
      let self = this;
      this.isLoading = true
      this.intervalID = setInterval(() => {
          if (i < n) {
            this.fetchBeers(true);
            i++;
          } else {
            this.isLoading=false;
            clearInterval(self.intervalID);
          }
        }, 1000
      )
    },
    open(n) {
      // line below helps us to able scroll:
      document.body.classList = 'details-open';
      this.currentBeerIndex = n;
    },
    close() {
      // line below disables scroll:
      document.body.classList = '';
      this.$router.replace("/");
    }
  },
  computed: {
    url() {
      const base = `https://api.punkapi.com/v2/beers?page=${this.pageIndex}&per_page=80`;
      return base;
    },
  },
  watch: {
    $route() {
      this.evaluatePlace();
    },
    fetchedBeers() {
      this.evaluatePlace();
    }
  }
}
</script>

<style lang="scss">
* {
  box-sizing: border-box;
}
body {
  margin: 0;
}
#app {
  position: relative;
  height: auto;
}
.details-open {
  overflow: hidden;
}
.home {
  overflow: hidden;
  background-color: rgb(229,229,229);
  .loading {
    display: flex;
    justify-content: center;
    align-items: center;
    position: fixed;
    background-color: rgba(255,255,255,0.95);
    top: 0;
    left: 0;
    min-height: 100vh;
    min-width: 100vw;
    z-index: 2;
    animation: rotate_beer linear infinite alternate;
    .spinning {
      animation: spin 3s infinite;
    }
  }
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;

}

@font-face {
  font-family: Against;
  src: url('./assets/font/against.otf');
}
#nav {
  background-color: rgba(100, 100, 100,0.6);
  height: 150px;
  h1 {
    margin: 0 0 0 2rem;
    padding: 1rem;
    font-family: Against;
    color: orange;
    font-size: 6rem;
    text-shadow: 2px 2px 0 #fff, -2px -2px 0 #fff, 2px 2px 3px rgba(0,0,0,0.3), -2px -2px 3px rgba(0,0,0,0.3);
  }
}
</style>
