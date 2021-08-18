<template>
  <div v-if="exam">
      <!-- Page Features-->
      <div class="row">
          <div class="col-lg-12">
              <div class="card bg-light border-0 h-100">
                  <div class="card-body text-center p-4 p-lg-5 pt-0 pt-lg-0">
                      <div class="feature bg-primary bg-gradient text-white rounded-3 mb-4 mt-n4"><i class="bi bi-collection"></i></div>
                      <h2 class="fs-4 fw-bold">{{ exam.name }}</h2>
                      <p class="mb-0">{{ exam.time_limit ? exam.time_limit + ' Min' : '0 Min'}}</p>
                      <NuxtLink :to="{path : '/test/exam/' + exam.id, query: { star: currentStar ? currentStar : false, upto : upTo, script : scriptStatus }}"><span>Start</span></NuxtLink>
                  </div>
              </div>
          </div>
      </div>
      <div class="row">

        <template>
          <div>
            <b-button v-b-toggle.sidebar-1>Course Menu</b-button>
            <b-sidebar id="sidebar-1" title="Course Menu" shadow>
              <div class="px-3 py-2">

                
                <!--reviews filter-->
                <div class="star-reviews-to-test">
                  <i class="icon-star-empty make-empty" @click="setStar(null)"></i>

                  <template v-if="upTo == true">
                    <i :class="{'icon-star' : currentStar >= star, 'icon-star-empty' : currentStar < star }" v-for="star in 5" @click="setStar(star)"></i>
                  </template>
                  <template v-if="upTo == false">
                    <i :class="{'icon-star' : currentStar == star, 'icon-star-empty' : currentStar != star }" v-for="star in 5" @click="setStar(star)"></i>
                  </template>

                  <i class="star-10" :class="{'icon-star' : upTo == true, 'icon-star-empty' : upTo == false }" @click="setUpTo()"></i>

                  <div class="code-notify">
                    <i class="icon-code" :class="{'icon-code-active' : scriptStatus}" @click.prevent="setScriptStatus()"></i>
                  </div>

                  <!-- <i class="icon-star"></i> -->
                </div> 
                
                <div v-for="chapter in exam.chapters">
                  <h6 class="sidebar-heading d-flex justify-content-between align-items-center px-3 mt-4 mb-1 text-muted">
                    <span>{{ chapter.name }}</span>
                    
                    <NuxtLink :to="{path : '/test/chapter/' + chapter.id, query: { star: currentStar ? currentStar : false, upto : upTo, script : scriptStatus }}" ><span class="badge badge-secondary">Start</span></NuxtLink>
                        
                    <a class="d-flex align-items-center text-muted" href="#" aria-label="Add a new report">
                      <span data-feather="plus-circle"></span>
                    </a>
                  </h6>
                  <ul class="nav flex-column mb-2">
                    <li class="nav-item" v-for="topic in chapter.topics">
                      <NuxtLink :to="{path : '/test/topic/' + topic.id, query: { star: currentStar ? currentStar : false, upto : upTo, script : scriptStatus }}" class="nav-link" href="#">
                        <span></span>
                        {{ topic.name }}
                        <span class="badge badge-secondary">Start Test</span>
                      </NuxtLink>
                    </li>
                  </ul>
                </div>  
              </div>
            </b-sidebar>
          </div>
        </template>


      </div>
  </div>
</template>
<script>
  export default {
    data(){
      return {
        loading : false,
        exam : null,
        currentStar : null,
        scriptStatus : false,
        upTo : false,
      }
    },
    methods : {
      fetchData(){
        var self = this;
        self.loading = true;
        var examId = self.$route.params.id;
        
        this.$axios.get('/exam/' + examId).then(( response ) => {
          self.exam = response.data.data;
          self.loading = false;
        }).catch((error) => {
          self.loading = false;
        })
      },
      setStar(star){
        if(star == null){
          this.upTo = false;
        } 
        this.currentStar = star;
      },
      setUpTo(){
        this.upTo = !this.upTo;
      },
      setScriptStatus(){
        this.scriptStatus = !this.scriptStatus;
      }
    },
    mounted(){
      this.fetchData();
    }
  }
</script>