<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences"
          >Load Submitted Experiences</base-button
        >
      </div>
      <ul v-if="!loading">
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
      <p v-if="loading">Loading...</p>
      <p v-if="err">{{ err }}</p>
      <p v-if="!loading && (!results || results.length === 0) && !err">
        No stored experiences found. Please add some.
      </p>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';

export default {
  components: {
    SurveyResult,
  },
  data() {
    return { results: [], loading: false, err: null };
  },
  methods: {
    loadExperiences() {
      this.loading = true;
      this.err = null;
      fetch(
        'https://vue-http-demo-d4177-default-rtdb.firebaseio.com/surveys.json'
      )
        .then((res) => {
          if (res.ok) {
            return res.json();
          }
        })
        .then((data) => {
          this.loading = false;
          const results = [];
          for (const id in data) {
            results.push({ id, name: data[id].name, rating: data[id].rating });
          }
          this.results = results;
        })
        .catch(() => {
          this.loading = false;
          this.err = 'Failed to fetch data -- please try again later';
        });
    },
  },
  mounted() {
    this.loadExperiences();
  },
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>
