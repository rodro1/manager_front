<template>
    <div>
      <div class="row justify-content-md-center">
        <div class="col-md-6 align-self-center">

            <div class="question-box-left-design">
                <div class="question-back-control">
                  <a @click.prevent="$router.go(-1)" class="btn btn-success" >Back</a>
                  <NuxtLink class="btn btn-warning" to="/exam">Back To Main</NuxtLink>
                </div>

                <div class="exam-star-reviews"  v-if="currentQuestion">
                  <div class="star-script-fitler">
                    <div class="star-reviews">
                      <i class='icon-star-empty make-empty' @click.prevent="saveStar(0)"></i>
                      <i @click.prevent="saveStar(star)" :class="{'icon-star-empty' : star > currentQuestion.star, 'icon-star' : star <=  currentQuestion.star}" v-for="star in 5"></i>
                    </div>
                    <div class="code-notify">
                      <i class="icon-code" :class="{'icon-code-active' : currentQuestion.script}" @click.prevent="enableScriptToDatabase()"></i>
                    </div>
                  </div>  
                </div>
                
            </div>
            <div class="question-box " v-if="end == false" >
              <form v-if="currentQuestion" >
                <h4 class="quiz-ques-head">{{currentQuestion.title}}</h4>
                <div class="form-group">
                  <label for="quiz-ans-head">Your Answer</label>
                  
                  <textarea class="quiz-ans-textarea form-control" v-model="currentQuestionAnswer" id="quiz-ans-head" @keydown.enter.exact.prevent @keyup.enter.exact="nextQuestion()" @keydown.enter.ctrl.exact="newline" @keydown.shift.ctrl.exact="forgotAnswer()"></textarea>
                </div>
                <button type="submit" class="btn btn-primary" @click.prevent="nextQuestion()" >OK</button>
                <button class="btn btn-warning" @click.prevent="prevQuestion()"  v-if="currentQuestionKey != 0">Prev</button>
                <a href="#" class="quiz-forgot-answer" @click.prevent="forgotAnswer()">Forgot Answer?</a href="#">
              </form>
            </div>

            <div class="question-box" v-if="end == true">
              <h1>Thankyou</h1>
              <button class="btn btn-warning" @click.prevent="prevQuestion()" >Prev</button>
              <button class="btn btn-primary" @click.prevent="showCorrectAnswer()" >Correct Answer</button>

              <ul class="my-exam-results">
                <li v-for="(question, index) in questions">
                  <h4 class="exam-result-question">{{ question.title }}</h4>
                  <p class="exam-result-answer" v-html="question.my_answer"></p>
                  <p v-if="showCorrectAnswerInRender" class="exam-result-correct-answer" v-html="question.answer"></p>
                </li>
              </ul>
            </div>
        </div> 
      </div>
      <div class="row">
        <div class="col-md-12">
          <div class="question-key-list" v-if="end == false">
              <ul>
                <li v-for="(question, index) in questions" :class="{'active' : currentQuestionKey == index, 'has-answer' : question.my_answer, 'has-star' : question.star > 0, 'has-code' : question.script}" @click.prevent="selectQuestion(index)">{{ index + 1}}</li>
              </ul>
            </div>
        </div>
      </div>
    </div>  
</template>
<script>
  export default {
    props: ['questions'],
    data(){
      return {
        currentQuestion : null,
        currentQuestionAnswer : '',
        currentQuestionKey : null,
        end : false,
        showCorrectAnswerInRender : false,
      }
    },
    methods : {
      saveStar(star){
        var self = this;
        this.$axios.post('/save-question-star/' + self.currentQuestion.id, {star : star}).then((response) => {
          if(response.data.status == 'success'){
            self.currentQuestion.star = star;
          }
        })
      },
      enableScriptToDatabase(){
        var self = this;
        var script_status = self.currentQuestion.script == 1 ? 0 : 1;
        this.$axios.post('/save-script-status/' + self.currentQuestion.id, {script : script_status}).then((response) => {
          if(response.data.status == 'success'){
            self.currentQuestion.script = script_status;
          }
        })
      },
      prepareQuestion(){
          this.currentQuestion = this.questions[0];
          this.currentQuestionKey = 0;
      },
      forgotAnswer(){
        alert(this.currentQuestion.answer);
      },
      showCorrectAnswer(){
        this.showCorrectAnswerInRender = !this.showCorrectAnswerInRender;
      },
      nextQuestion(){
        if(!this.currentQuestionAnswer){
          alert('Must have to give answer');
          return;
        }

        this.questions[this.currentQuestionKey]['my_answer'] = this.currentQuestionAnswer;
        
        this.currentQuestionAnswer = "";
        this.currentQuestionKey += 1;

        if(this.questions.length == this.currentQuestionKey){
          this.end = true;
          return;
        }
        
        this.currentQuestion = this.questions[this.currentQuestionKey];
      },
      selectQuestion(key){
        this.currentQuestionKey = key;
        this.currentQuestion = this.questions[this.currentQuestionKey];
      },
      prevQuestion(){
        if(this.currentQuestionKey == 0){
          alert('No previous question have');
          return;
        }

        this.currentQuestionKey -= 1;
        this.currentQuestion = this.questions[this.currentQuestionKey];
        this.currentQuestionAnswer = this.currentQuestion.my_answer;
        this.end = false;

      },
      newline(){
        this.currentQuestionAnswer = `${this.currentQuestionAnswer}\n`;
      }
    },
    mounted(){
      this.prepareQuestion();

    }
  }
</script>