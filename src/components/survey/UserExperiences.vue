<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperience">Load Submitted Experiences</base-button>
      </div>
      <p v-if="isLoading">Loading...</p>
      <p v-else-if="!isLoading && error">
      {{ error }}
      </p>
      <p v-else-if="!isLoading && (!results || results.length === 0)">No stored experiences found. Start adding some survey results first.</p>
      <ul v-else>
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';

export default {
  components: {
    SurveyResult,
  },
  data(){
    return{
      results:[],
      isLoading: false,
      error: null,
    }
  },
  methods:{
    loadExperience(){
      this.isLoading = true;
      this.error = null;
      fetch('https://vue-http-e7631-default-rtdb.firebaseio.com/survaeys.json')
        .then((respons) => {
          if(respons.ok){
            return respons.json();
          }
        })
        .then((data) =>{
          this.isLoading = false;
          const results = [];
          for(const id in data){
            results.push({
              id: id, 
              name: data[id].name,
              rating : data[id].rating,
            });
          }
          this.results = results;
        })
       .catch(() =>{
          this.isLoading = false;
          this.error = 'Failed to fetch data - please try again later.';
       });
    },
  },
  mounted(){
    this.loadExperience();
  }
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>