<template>
  <div class="container">

      <div v-if="exam">
          <h2 class="question-box-root-title">{{ exam.name }}: Questions</h2>
          <exam-questions :questions="exam.questions"></exam-questions>
      </div>
    
  </div>
</template>
<script>
  export default {
    data(){
      return {
        exam : null,
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

        this.$axios.get('exam/'+id+'/with-questions?' + queryString).then(( response ) => {
          self.exam = response.data.data;
        });
      },

    },
    mounted(){
      this.fetchData();

    }
  }
</script>