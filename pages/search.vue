<template>
  <ais-instant-search-ssr>
    <ais-search-box />
    <ais-hits>
      <div slot="item" slot-scope="{ item }">
        <h2>{{ item.name }}</h2>
      </div>
    </ais-hits>
  </ais-instant-search-ssr>
</template>

<script>
import {
  AisInstantSearchSsr,
  createServerRootMixin,
  AisHits,
  AisSearchBox
} from "vue-instantsearch";
import algoliasearch from "algoliasearch/lite";
import "instantsearch.css/themes/algolia-min.css";

const searchClient = algoliasearch(
  "latency",
  "6be0576ff61c053d5f9a3225e2a90f76"
);

function nuxtRouter(vueRouter) {
  return {
    read() {
      return vueRouter.currentRoute.query;
    },
    write(routeState) {
      vueRouter.push({
        query: routeState
      });
    },
    createURL(routeState) {
      return vueRouter.resolve({
        query: routeState
      }).href;
    },
    onUpdate(cb) {
      if (typeof window === "undefined") return;

      this._onPopState = event => {
        const routeState = event.state;
        // On initial load, the state is read from the URL without
        // update. Therefore, the state object isn't present. In this
        // case, we fallback and read the URL.
        if (!routeState) {
          cb(this.read());
        } else {
          cb(routeState);
        }
      };
      window.addEventListener("popstate", this._onPopState);
    },
    dispose() {
      if (typeof window === "undefined") return;

      window.removeEventListener("popstate", this._onPopState);
    }
  };
}

export default {
  components: {
    AisInstantSearchSsr,
    createServerRootMixin,
    AisHits,
    AisSearchBox
  },
  data() {
    const mixin = createServerRootMixin({
      searchClient,
      indexName: "instant_search",
      routing: {
        router: nuxtRouter(this.$router)
      }
    });
    return {
      ...mixin.data()
    };
  },
  provide() {
    return {
      // Provide the InstantSearch instance for SSR
      $_ais_ssrInstantSearchInstance: this.instantsearch
    };
  },
  serverPrefetch() {
    return this.instantsearch.findResultsState(this).then(algoliaState => {
      this.$ssrContext.nuxt.algoliaState = algoliaState;
    });
  },
  beforeMount() {
    const results =
      this.$nuxt.context.nuxtState.algoliaState || window.__NUXT__.algoliaState;

    this.instantsearch.hydrate(results);
  }
};
</script>
