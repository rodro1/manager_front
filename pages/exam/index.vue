<template>
  <div class="container">
      <div class="row">
        <h3>All Exams</h3>
        <table class="table all-exam-table" :class="{'is_loading' : loading}">
          <thead>
            <tr>
              <th scope="col">#</th>
              <th scope="col">Name</th>
              <th scope="col">Time</th>
              <th scope="col">Action</th>
            </tr>
          </thead>
          <tbody v-if="exams" >
            <tr v-for="exam in exams.data">
              <th scope="row">1</th>
              <td><NuxtLink :to="'/exam/' + exam.id">{{ exam.name }}</NuxtLink></td>
              <td>{{ exam.time_limit ? exam.time_limit + ' Min' : '0 Min'}}</td>
              <td><NuxtLink :to="'/exam/' + exam.id" class="btn btn-primary">Start</NuxtLink></td>
            </tr>
          </tbody>
        </table>
      </div>
  </div>
</template>
<script>
  export default {
    data(){
      return {
        exams : null,
        loading : false
      }
    },
    methods : {
      fetchData(){
        var self = this;
        self.loading = true;
        this.$axios.get('/get-exams').then(( response ) => {
          self.exams = response.data;
          self.loading = false;
        }).then((error) => {
          self.loading = false;
        });
      }
    },
    mounted(){
      this.fetchData();
    }
  }
</script>