<template>
      <div class="row entry-question">
        <div class="col-md-3">
          <div class="entry-question-sidebar">

            <form class="entry-sidebar-filter">
              <div class="form-group" :class="{'is_loading_no_height' : loading}">
                <label for="exam-list">Exams</label>

                  <span class="add-span-category" @click="showAddCatExamFieldNow()">+</span>

                  <template v-if="showAddCatExamField">
                      <input type="text" v-model="catExam"
                      @keydown.enter.exact.prevent 
                      @keyup.enter.exact="addCatExam()" 
                    >
                      <span class="add-span-category" @click="addCatExam()">Ok</span>
                  </template>

                 
                <select @change="selectExam($event)" class="form-control" id="exam-list" v-model="selectedExamIndex" placeholder="Select Exam">
                  <option value="">Select One</option>
                  <option v-for="(exam, index) in exams" :value="index" >{{ exam.name }}</option>
                </select>

              </div>

              <div class="form-group" :class="{'is_loading_no_height' : loading}" v-if="selectedExamIndex === 0 || selectedExamIndex" >
                <label for="chapter-list">Chapter</label>
                

                <span class="add-span-category" @click="showAddCatChapterFieldNow()">+</span>

                <template v-if="showAddCatChapterField">
                  <input type="text" v-model="catChapter"
                   @keydown.enter.exact.prevent 
                    @keyup.enter.exact="addCatChapter()" 
                    >
                  <span class="add-span-category" @click="addCatChapter()">Ok</span>
                </template>

                <select @change="selectChapter($event)" v-model="selectedChapterIndex" class="form-control" id="chapter-list" >
                  <option value="">Select One</option>
                  <option v-for="(chapter, index) in examChapters" :value="index" >{{ chapter.name }}</option>
                </select>
              </div>

              <div class="form-group" :class="{'is_loading_no_height' : loading}" v-if="selectedChapterIndex === 0 || selectedChapterIndex">
                <label for="Topic">Topic</label>


                <span class="add-span-category" @click="showAddCatTopicFieldNow">+</span>

                <template v-if="showAddCatTopicField">
                    <input type="text" v-model="catTopic"
                   @keydown.enter.exact.prevent 
                    @keyup.enter.exact="addCatTopic()" 
                    >
                  <span class="add-span-category" @click="addCatTopic()">Ok</span>
                </template>

                


                <select @change="selectTopic($event)" class="form-control" v-model="selectedTopicIndex" id="Topic">
                  <option value="">Select One</option>
                  <option v-for="(topic, index) in examTopics" :value="index" >{{ topic.name }}</option>
                </select>
              </div>

            </form>


            <!-- END MENU -->
          </div>
        </div>
        <div class="col-md-6">
          <div class="entry-question-content">
            <form v-if="selectedExam && selectedChapter && selectedTopic">


              <div class="import-star-reviews">
                  <div class="star-reviews">
                    <i class='icon-star-empty make-empty' @click.prevent="setStar(0)"></i>
                    <i @click.prevent="setStar(star)" :class="{'icon-star-empty' : star > postData.star, 'icon-star' : star <=  postData.star}" v-for="star in 5"></i>
                  </div>
                  <div class="code-notify">
                    <i class="icon-code" :class="{'icon-code-active' : postData.script}" @click.prevent="enableScript()"></i>
                  </div>
                </div>
              


              <div class="form-group">
                <label for="question">Question</label>

                


                <input type="text" ref="question" class="form-control" id="question" v-model="postData.question">




              </div>
              <div class="form-group">
                <label for="answer">Answer</label>
                <textarea class="form-control" id="answer" rows="3" v-model="postData.answer" @keydown.enter.exact.prevent @keyup.enter.exact="saveQuestion()" @keydown.enter.ctrl.exact="newline"></textarea>
              </div>
              <button class="btn btn-warning" @click.prevent="saveQuestion()">{{ buttonText }}</button>
            </form>
          </div>
        </div>

        <div class="col-md-3">
          <div class="entry-question-sidebar" :class="{'is_loading' : loading}">
            <ul class="question-list" v-if="selectedTopicQuestions">
              <li v-for="(question, index) in selectedTopicQuestions">
                <div class="question-list-number">{{ index + 1}}</div>
                <div class="question-list-content-area">
                  <div class="question-list-icons">
                    <button class="btn btn-warning btn-sm" @click.prevent="editQuestion(question)">Edit</button>
                    <button class="btn btn-success btn-sm" @click.prevent="deleteQuestion(question)">Del</button>
                  </div>
                  <div class="question-list-content">
                    <h4>{{ question.title }}</h4>
                    <p v-html="question.answer"></p>
                  </div>
                </div>
                
              </li>
            </ul>
          </div>
        </div>

      </div>

</template>
<script>
  export default {
    data(){
      return {

        loading : false,

        exams : null,
        examChapters : null,
        examTopics : null,


        selectedExamIndex : '',
        selectedChapterIndex : '',
        selectedTopicIndex : '',


        selectedExam : null,
        selectedChapter : null,
        selectedTopic : null,

        selectedQuestion : null,

        postData : {
          question : '',
          answer : '',
          star : '',
          script : '',
        },

        selectedTopicQuestions : {},

        buttonText : 'Save',


        catExam : '',
        catChapter : '',
        catTopic : '',
        
        showAddCatExamField : false,
        showAddCatChapterField : false,
        showAddCatTopicField : false,

      }
    },
    methods : {
      setStar(star){
        this.postData.star = star;
      },
      enableScript(){
        this.postData.script = this.postData.script ? 0 : 1;
      },
      fetchData(){
        var self = this;
        self.loading = true;
        return this.$axios.get('/get-exams').then((response) => {
          self.exams = response.data.data;
          self.loading = false;
        }).catch((error) => {
          self.loading = false;
        })
      },
      addCatExam(){
        var self = this;
        self.loading = true;
        if(self.catExam == ''){
          alert('Exam text field empty');
          return;
        }
        this.$axios.post('/add-cat-exam', {catExam : self.catExam}).then((response) => {
          self.fetchData();
          self.catExam = '';
        })
      },
      addCatChapter(){
        var self = this;
        self.loading = true;
        if(self.catChapter == ''){
          alert('Chapter text field empty');
          return;
        }
        this.$axios.post('/add-cat-chapter', {examId : self.selectedExam.id, catChapter : self.catChapter}).then((response) => {
          self.fetchData().then(() => {
            self.selectExam(self.selectedExamIndex, 'index');
            self.catChapter = '';
          });
        })

      },
      addCatTopic(){
        var self = this;
        self.loading = true;
        if(self.catTopic == ''){
          alert('Topic text field empty');
          return;
        }
        this.$axios.post('/add-cat-topic', {examId : self.selectedExam.id, chapterId : self.selectedChapter.id,catTopic : self.catTopic}).then((response) => {
          self.fetchData().then(() => {
            self.selectExam(self.selectedExamIndex, 'index');
            self.selectChapter(self.selectedChapterIndex, 'index');
            self.catTopic = '';
          })
        })
      },
      editQuestion(question){
        this.selectedQuestion = question;
        this.postData.question = question.title;
        this.postData.answer = question.answer;
        this.postData.star = question.star;
        this.postData.script = question.script;
      },
      deleteQuestion(question){
        var self = this;
        self.loading = true;
        var confirm = window.confirm("Are you sure you want to delete?");
        if(confirm){
          this.$axios.get('/delete/question/' + question.id).then((response) => {

            self.fetchTopicQuestion(this.selectedTopic.id);

            if(response.status = 'success'){
                //  self.$bvToast.toast('Deleted Successfully', {
                //   title: `Success`,
                //   solid: true
                // })
             } 
           
          });
        }



      },
      saveQuestion(){
        var self = this;
        self.buttonText = 'Saving..';
        if(!this.postData.question || !this.postData.answer){
          alert('Question or answer not be empty');
          return;
        }


        self.loading = true;

        var selectedQuestionId = this.selectedQuestion ? this.selectedQuestion.id : '';
        this.$axios.post('/save-question?exam_id=' + this.selectedExam.id + '&chapter_id=' + this.selectedChapter.id + '&topic_id=' + this.selectedTopic.id + '&question_id=' + selectedQuestionId, this.postData).then((response) => {
          if(response.status = 'success'){
            self.resetPostData();
            

            self.fetchTopicQuestion(this.selectedTopic.id);

            self.$refs.question.focus();

            self.buttonText = 'Save';

            if(this.selectedQuestion){
              // self.$bvToast.toast('Question updated successfully', {
              //   title: `Success`,
              //   solid: true
              // })
            } else {
              // self.$bvToast.toast('Question created successfully', {
              //   title: `Success`,
              //   solid: true
              // })
            }
            
            this.selectedQuestion = null;


          }
        }).catch((error) => {

          // self.$bvToast.toast('Something has wrong', {
          //     title: `Error`,
          //     solid: true
          //   })


          this.selectedQuestion = null;
          self.buttonText = 'Save';
        });
      },
      newline(){
        this.postData.answer = `${this.postData.answer}\n`;
      },
      selectExam(eventOrValue, valueGoing='event'){
        if(valueGoing == 'event'){
          var index = eventOrValue.target.value;
        } else {
          var index = eventOrValue;
        }
        if(index === ''){
          this.examChapters = null;
          this.selectedChapterIndex = '';

          this.examTopics = null;
          this.selectedTopicIndex = '';


          this.selectedExam = null;
          this.selectedChapter = null;
          this.selectedTopic = null;

          return;

        }
        this.selectedExam = this.exams[index];
        this.examChapters = this.exams[index]['chapters'];
        // console.log(this.exams[index]['chapters']);
      },

      selectChapter(eventOrValue, valueGoing='event'){
        if(valueGoing == 'event'){
          var index = eventOrValue.target.value;
        } else {
          var index = eventOrValue;
        }
        if(index === ''){
          this.examTopics = null;
          this.selectedTopicIndex = '';

          this.selectedChapter = null;
          this.selectedTopic = null;

          return;
        }

        this.selectedChapter = this.examChapters[index];
        this.examTopics = this.examChapters[index]['topics'];
      },

      selectTopic(eventOrValue, valueGoing='event'){
        if(valueGoing == 'event'){
          var index = eventOrValue.target.value;
        } else {
          var index = eventOrValue;
        }
        if(index === ''){

          this.selectedTopic = null;

          return;
        }

        this.selectedTopic = this.examTopics[index];
        this.fetchTopicQuestion(this.selectedTopic.id);
      },

      fetchTopicQuestion(topicId){
        var self = this;
        self.loading = true;
        this.$axios.get('/exam-topic/' + topicId + '/with-questions').then(( response ) => {
          self.selectedTopicQuestions = response.data.data.questions;
          self.loading = false;
        }).catch((error) => {
          self.loading = false;
        });
      },
      showAddCatExamFieldNow(){
        this.showAddCatExamField = !this.showAddCatExamField;
      },
      showAddCatChapterFieldNow(){
        this.showAddCatChapterField = !this.showAddCatChapterField;
      },
      showAddCatTopicFieldNow(){
        this.showAddCatTopicField = !this.showAddCatTopicField;
      },
      resetPostData(){
        this.postData.question = '';
        this.postData.answer = '';
        this.postData.star = '';
        this.postData.script = '';
      }

    },
    mounted(){
      this.fetchData();
    }
  }
</script>
