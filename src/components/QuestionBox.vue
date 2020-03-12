<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template v-slot:lead>{{ currentQuestion.question }}</template>
      <hr class="my-4" />

      <b-list-group >
        <b-list-group-item :class="answerClass(index)"
         @click="selectAnswer(index)" v-for="(answer, index) in answers" :key="index">{{ answer }}</b-list-group-item>
      </b-list-group>
      <b-button variant="primary"
      @click="submitAnswer"
      :disabled="selectedIndex === null || answered">
        Submit
        </b-button>
      <b-button @click="next" variant="success" href="#">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from 'lodash'

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },
  data () {
    return {
      selectedIndex: null,
      shuffledAnswers: [],
      answered: false,
      correctIndex: null
    }
  },
  computed: {
    answers () {
      const answers = [...this.currentQuestion.incorrect_answers]
      console.log(answers)
      answers.push(this.currentQuestion.correct_answer)
      console.log(answers)
      return answers
    }
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler () {
        this.selectedIndex = null
        this.answered = false
        this.shuffleAnswers()
      }
    }
  },
  methods: {
    selectAnswer (index) {
      this.selectedIndex = index
    },
    submitAnswer () {
      let isCorrect = false
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true
      }
      this.increment(isCorrect)
      this.answered = true
    },
    shuffleAnswers () {
      const answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
      this.shuffledAnswers = _.shuffle(answers)
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
    },
    answerClass (index) {
      let answerClass = ''
      if (!this.answered && this.selectedIndex === index) {
        answerClass = 'selected'
      } else if (this.answered && this.correctIndex === index) {
        answerClass = 'correct'
      } else if (this.answered && this.selectedIndex === index && this.correctIndex !== index) {
        answerClass = 'incorrect'
      }
      return answerClass
    }
  }
}
</script>
<style scoped>
.list-group {
  margin-bottom: 15px
}
.btn {
  margin: 0 5px
}
.list-group-item:hover {
  background: #EEE;
  cursor: pointer;
}
.selected {
  background: lightblue;
  }

.correct {
  background: lightgreen;
}

.incorrect {
  background: red;
}
</style>
