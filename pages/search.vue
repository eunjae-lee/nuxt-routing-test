<template>
  <div>
    <ais-search-box />
    <ais-hits>
      <div slot="item" slot-scope="{ item }">
        <h2>{{ item.name }}</h2>
      </div>
    </ais-hits>
  </div>
</template>

<script>
import { AisHits, AisSearchBox } from "vue-instantsearch";
import "instantsearch.css/themes/algolia-min.css";

export default {
  components: {
    AisHits,
    AisSearchBox
  }
};
</script>
