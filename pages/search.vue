<template>
  <ais-instant-search :search-client="searchClient" index-name="instant_search">
    <ais-search-box />
    <ais-hits>
      <div slot="item" slot-scope="{ item }">
        <h2>{{ item.name }}</h2>
      </div>
    </ais-hits>
  </ais-instant-search>
</template>

<script>
import { AisInstantSearch, AisHits, AisSearchBox } from "vue-instantsearch";
import algoliasearch from "algoliasearch/lite";
import "instantsearch.css/themes/algolia-min.css";

const searchClient = algoliasearch(
  "latency",
  "6be0576ff61c053d5f9a3225e2a90f76"
);

export default {
  components: {
    AisInstantSearch,
    AisHits,
    AisSearchBox
  },
  data() {
    return {
      searchClient
    };
  }
};
</script>
