<template>
  <section>
    <BaseCard>
      <h2>Submitted Experiences</h2>
      <div>
        <BaseButton @click="loadExperinces"
          >Load Submitted Experiences</BaseButton
        >
      </div>
      <p v-if="isLoading">Loading...</p>
      <p v-else-if="!isLoading && error">{{ error }}</p>
      <p v-else-if="!isLoading && (!results || results.length === 0)">
        No stored experiences found. Start adding some survey results
      </p>
      <ul v-else>
        <SurveyResult
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></SurveyResult>
      </ul>
    </BaseCard>
  </section>
</template>

<script>
import SurveyResult from "./SurveyResult.vue";
import BaseButton from "@/components/UI/BaseButton.vue";
import BaseCard from "@/components/UI/BaseCard.vue";

export default {
  components: {
    SurveyResult,
    BaseButton,
    BaseCard,
  },
  data() {
    return {
      results: [],
      isLoading: false,
      error: null,
    };
  },
  methods: {
    loadExperinces() {
      this.isLoading = true;
      this.error = null;
      fetch("https://vue-http-demo-e59de-default-rtdb.firebaseio.com/surveys.json")
        .then((response) => {
          if (response.ok) {
            return response.json();
          }
        })
        .then((data) => {
          this.isLoading = false;
          const results = [];
          for (const id in data) {
            results.push({
              id: id,
              name: data[id].name,
              rating: data[id].rating,
            });
          }
          this.results = results;
        })
        .catch((error) => {
          console.log(error);
          this.isLoading = false;
          this.error = "Failed to fetch data -Please try again later!";
        });
    },
  },
  mounted() {
    this.loadExperinces();
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
