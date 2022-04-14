<template>
  <div class="main">
    <div class="form">
      <span>Search for NPM package:</span>
      <input @keyup.enter="triggerSearch" v-model="searchQuery" />
      <button @click="triggerSearch">Search</button>
    </div>
    <div class="result">
      <h3>Search result</h3>
      <ul>
        <li
          class="result__record"
          v-for="(record, index) in searchResult"
          :key="index"
        >
          <span>{{ record.name }}</span>
          <span>{{ record.version }}</span>
          <span>{{ record.authorName }}</span>
        </li>
      </ul>
    </div>
  </div>
</template>

<style lang="css">
.main {
  text-align: left;
  max-width: 75%;
  margin: 2rem auto;
}

.form {
  display: flex;
  gap: 1rem;
  /* justify-content: center; */
}

.result {
  text-align: left;
}

.result__record {
  display: flex;
  gap: 1rem;
}

.result__record span:first-child {
  width: 24rem;
}

.result__record span:nth-child(2) {
  width: 4rem;
}
</style>

<script lang="ts">
import { defineComponent } from "vue";
import axios from "axios";

interface NpmPackage {
  package: {
    author?: {
      name: string;
    };
    name: string;
    version: string;
  };
}

export default defineComponent({
  data() {
    return {
      searchQuery: "",
      searchResult: {},
    };
  },
  methods: {
    async triggerSearch() {
      console.log("gonna search with", this.searchQuery);
      const response = await axios({
        method: "GET",
        url: "https://api.npms.io/v2/search",
        params: {
          q: this.searchQuery,
        },
      });

      this.searchResult = response.data.results.map((record: NpmPackage) => {
        console.log(record);
        const { author, name, version } = record.package;
        return {
          name,
          authorName: author?.name || "unknown",
          version,
        };
      });
    },
  },
  mounted() {
    console.log("Test page component");
  },
});
</script>
