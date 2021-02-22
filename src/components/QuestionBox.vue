<template>
  <div class="">
    <div>
      <b-jumbotron >

        <template slot="lead">
          {{currentQuestion.question}}
        </template>

        <hr class="my-4">

        <b-list-group>
          <b-list-group-item v-for="(answer,index) in shuffledAnswers" :key="index"
           @click="selectAnswer(index)"
           :class="answerClass(index)">
          {{ answer }}
          </b-list-group-item>
        </b-list-group>

        <b-button variant="primary" @click="submitAnswer"  :disabled=" selectedIndex === null || answered">Submit</b-button>
        <b-button @click="next" :disabled="counter===(totalQuestions-1)" variant="success" href="#">Next</b-button>
      </b-jumbotron>
    </div>
  </div>
</template>

<script>
import  _ from 'lodash'
export default {
  props:{
    totalQuestions :{
      type: Number
    },
    counter :{
      type: Number
    },
    currentQuestion: Object,
    next:Function,
    increment:Function
  },
  data(){
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered:false,
      questionNum:0

    }
  },
  computed:{
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers]
      answers.push(this.currentQuestion.correct_answer)
      return answers
    }
  },
  watch:{
    currentQuestion:{
      immediate:true,
      handler(){
        this.selectedIndex = null
        this.answered=false
        this.shuffleAnswers()
      }
    }
  },
  methods: {
    selectAnswer(index){
      this.selectedIndex = index
    },
    submitAnswer(){
      let isCorrect = false

      if(this.selectedIndex === this.correctIndex){
        isCorrect = true
      }
      this.answered=true
      this.increment(isCorrect)
    },
    shuffleAnswers(){
      let answersCopy = [...this.currentQuestion.incorrect_answers,this.currentQuestion.correct_answer]
      this.shuffledAnswers = _.shuffle(answersCopy)
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
    },
    answerClass(index){
      let answerClass = ''

      if(!this.answered && this.selectedIndex === index){
        answerClass = 'selected';
      } else if (this.answered && this.correctIndex === index){
        answerClass = 'correct';
      } else if((this.answered )&& (this.selectedIndex === index) && (this.correctIndex !== index)){
          answerClass = 'incorrect';
        }
      
      return answerClass
    }
  }
}
</script>

<style scoped>
.list-group{
  margin-bottom: 15px;
}
.btn {
  margin: 0 5px;
}
.list-group-item:hover{
  background: grey;
  cursor: pointer;
}
.selected {
    background: lightblue;
}
.correct {
    background: rgb(71, 161, 71);
}
.incorrect {
    background: red;
}
</style>
