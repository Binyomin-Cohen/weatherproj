<template>
  <div id="previousSearchWrapper" class="previousSearches">
      <button  v-on:click="getPreviousSearches" class="btn btn-primary">Show Previous Searches</button>
      <div v-if="showPreviousSearches">
          <p v-for="pSearch in previousSearchesArray">{{pSearch}}</p>
      </div>

      
  </div> 
</template>

<script>
export default {
  name: 'PreviousSearches',
  data () {
    return {
      previousSearchesArray: [],
      showPreviousSearches : false
    }
  },
  methods:{
      getPreviousSearches: function(){
          if(this.showPreviousSearches){
              this.showPreviousSearches = false;
          } 
          else{
            var pSearches = localStorage.previousSearches;
            var pSearchesArray = pSearches.substring(0, pSearches.length-1).split(",");
            this.previousSearchesArray = pSearchesArray;
            this.showPreviousSearches = true;
          }
      }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    #previousSearchWrapper{
        width: 400px;
        max-width: 100%;
        font-size: 1.3em;
        margin:auto;
    }
</style>
