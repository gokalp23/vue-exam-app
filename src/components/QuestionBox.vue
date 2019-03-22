<template>
  <div class="question-box-container">
    <b-jumbotron class="mt-5">
      <template>
        <div slot="lead" v-html="currentQuestion.question">
          {{ currentQuestion.question }}
        </div>
      </template>

      <hr class="my-4">
      
      <b-list-group>
        <b-list-group-item 
        v-for="(answer,index) in shuffledAnswers" 
        :key="index"
        v-html="answer"
        @click.prevent="selectAnswer(index)"
        :class="answerClass(index)"
        >
          {{ answer }}
        </b-list-group-item>
      </b-list-group>

      <hr class="my-4">

      <b-button-group vertical>
        <b-button 
        variant="success" 
        @click="submitAnswer"
        :disabled="selectedIndex === null || isAnswered"
        >Submit</b-button>
        <b-button class="my-2" @click="nextQuestion" variant="primary">Next</b-button>
      </b-button-group>
      
    </b-jumbotron>
  </div>
</template>
<script>
import _ from 'lodash';

export default {
  props: {
    currentQuestion: Object,
    nextQuestion: Function,
    increment: Function
  },
  data(){
    return {
      selectedIndex: null,
      correctIndex : null,
      shuffledAnswers: [],
      isAnswered: false
    }
  },
  computed: {
    allAnswers() {
      let answers = [...this.currentQuestion.incorrect_answers]
       answers.push(this.currentQuestion.correct_answer)
       return answers
    }
  },
  watch: {
    currentQuestion() {
      this.selectedIndex = null,
      this.isAnswered = false,
      this.shuffleAnswers()
    }
  },
  methods: {
    selectAnswer(index){
      this.selectedIndex = index;
    },
    shuffleAnswers(){
      let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
      this.shuffledAnswers = _.shuffle(answers)
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
    },
    submitAnswer(){
      let isCorrect = false;
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.isAnswered = true;
      this.increment(isCorrect)
    },
    answerClass(index){
      let answerClass = ''
      if(!this.isAnswered && this.selectedIndex === index){
        answerClass = 'selectedListItem'
      }
      else if (this.isAnswered && this.selectedIndex === index && this.correctIndex === index) {
        answerClass = 'correctListItem'
      }
      else if (this.isAnswered && this.selectedIndex === index && this.correctIndex !== index){
        answerClass = 'wrongListItem'
      }

      return answerClass
    }
  },
  mounted(){
    this.shuffleAnswers()
  }
}
</script>

<style scoped>
  .list-group-item:hover {
    background: lightgrey;
    cursor: pointer;
  }

  .selectedListItem {
    background-color: turquoise;
  }

  .correctListItem {
    background-color: green;
  }

  .wrongListItem {
    background-color: red;
  }

</style>

