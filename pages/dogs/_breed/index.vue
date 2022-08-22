<template>
  <section class="container">
    <div class="columns is-multiline">
      <div v-for="(item, i) in dog_list" v-bind:key="i" class="column is-2">
        <img v-bind:src="item.url" />

        <span v-if="i < 3" class="tag is-danger">NEW</span>
        <a class="button is-warning is-small" v-on:click="item.like += 1">
          <span>いいね！{{ item.like }}件</span>
        </a>
      </div>
    </div>

    <nav class="pagination" role="navigation" aria-label="pagination">
      <ul class="pagination-list">
        <li v-for="count in page_count" :key="count">
          <nuxt-link
            class="pagination-link"
            :class="{ 'is-current': current == count }"
            :to="{ path: '?page=' + count }"
            append
          >
            {{ count }}
          </nuxt-link>
        </li>
      </ul>
    </nav>
  </section>
</template>

<script>
import dogApi from "@/api/dog";
import { mapState } from "vuex";

export default {
  watchQuery: ["page"],
  validate({ params }) {
    return /^[a-z]+$/.test(params.breed);
  },
  data: function () {
    return {
      current: 1,
    };
  },
  asyncData: function (context) {
    return {
      current: parseInt(context.query["page"]) || 1,
    };
  },
  fetch: async function ({ store, params, query }) {
    const page = parseInt(query["page"]) || 1;
    const start = 20 * (page - 1);
    const end = start + 20;

    const json = await dogApi.dogs(params.breed);

    store.commit("page_count_update", Math.ceil(json.length / 20));
    store.commit("dog_list_update", json.slice(start, end));
  },
  computed: mapState(["page_count", "dog_list"]),
};
</script>
