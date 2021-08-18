<template>
  <div class="container">

        
      <div v-if="chapters">
          
          <h2 class="question-box-root-title">{{ chapters.name }}: Questions</h2>
          <exam-questions :questions="chapters.questions"></exam-questions>

      </div>
    
  </div>
</template>
<script>
  export default {
    data(){
      return {
        chapters : null,
      }
    },
    methods : {
      fetchData(){
        var self = this;
        var id = self.$route.params.id;

        var star = self.$route.query.star;
        var upto = self.$route.query.upto;
        var script = self.$route.query.script;
        
        var queryString = 'star=' + star + '&upto=' + upto + '&script=' + script;

        this.$axios.get('/exam-chapter/' + id + '/with-questions' + '?' + queryString).then(( response ) => {
          self.chapters = response.data.data;
        });
      },

    },
    mounted(){
      this.fetchData();

    }
  }
</script>