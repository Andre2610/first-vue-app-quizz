<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template v-slot:lead>
        {{ currentQuestion.question }}
      </template>

      <hr class="my-4" />

      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in shuffledAnswers"
          :key="index"
          :disabled="answered"
          @click="selectAnswer(index)"
          :class="answerClass(index)"
          >{{ answer }}</b-list-group-item
        >
      </b-list-group>

      <b-button
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex === null || answered"
        >Submit</b-button
      >
      <b-button @click="next" variant="success">Next</b-button></b-jumbotron
    >
  </div>
</template>

<script>
import _ from "lodash";
export default {
  props: { currentQuestion: Object, next: Function, increment: Function },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false,
    };
  },
  computed: {
    answers() {
      const answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer,
      ];
      return answers;
    },
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.answered = false;
        this.shuffleAnswers();
      },
    },
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    shuffleAnswers() {
      const answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer,
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(
        this.currentQuestion.correct_answer
      );
    },
    submitAnswer() {
      let isCorrect = false;
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.answered = true;
      this.increment(isCorrect);
    },
    answerClass(index) {
      let answerClass = "";

      if (!this.answered && this.selectedIndex === index) {
        answerClass = "selected";
      } else if (this.answered && this.correctIndex === index) {
        answerClass = "correct";
      } else if (
        this.answered &&
        this.selectedIndex === index &&
        this.correctIndex !== index
      ) {
        answerClass = "incorrect";
      } else {
        answerClass = "";
      }

      return answerClass;
    },
  },
  //   mounted() {
  //     this.shuffleAnswers();
  //   },
};
</script>

<style scoped>
.list-group {
  margin-bottom: 2em;
}
.list-group-item:hover {
  background: rgb(194, 194, 194);
  transform: scale(1.05);
  z-index: 2;
  cursor: pointer;
}
.selected {
  background-color: rgb(90, 175, 255);
}
.correct {
  background-color: rgb(88, 182, 88);
}
.incorrect {
  background-color: rgb(246, 82, 82);
}
.btn {
  margin: 0 1em;
}
</style>
