<template>
  <div class="container">

      <div v-if="topic">
          <h2 class="question-box-root-title">{{ topic.name }}: Questions</h2>
          <exam-questions :questions="topic.questions"></exam-questions>
      </div>
    
  </div>
</template>
<script>
  export default {
    data(){
      return {
        topic : null,
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

        this.$axios.get('/exam-topic/' + id + '/with-questions?' + queryString).then(( response ) => {
          self.topic = response.data.data;
        });
      },

    },
    mounted(){
      this.fetchData();

    }
  }
</script>