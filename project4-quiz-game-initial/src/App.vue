<template>
  <div>
    <ScoreBoard :winCount="this.winCount" :loseCount="this.loseCount" />

    <div v-if="this.question">
      <h1 v-html="this.question"></h1>

      <div v-for="(answer, index) in this.answers" v-bind:key="index">
        <input
          :disabled="this.answerSubmitted"
          type="radio"
          name="options"
          :value="answer"
          v-model="this.chosen_answer"
        />

        <label v-html="answer"></label><br />
      </div>

      <button
        v-if="!this.answerSubmitted"
        @click="this.submitAnswer()"
        class="send"
        type="button"
      >
        Send
      </button>

      <section class="result" v-if="this.answerSubmitted">
        <div v-if="this.chosen_answer == this.correctAnswer">
          <h4
            v-html="
              '&#9989; Congratulations, the answer ' +
              this.correctAnswer +
              ' is correct.'
            "
          ></h4>
        </div>
        <div v-else>
          <h4
            v-html="
              `&#10060; I'm sorry, you picked the wrong answer. The correct is ` +
              this.correctAnswer +
              '.'
            "
          ></h4>
        </div>
        <button @click="this.getNewQuestion()" class="send" type="button">
          Next question
        </button>
      </section>
    </div>
  </div>
</template>

<script>
import ScoreBoard from "./components/ScoreBoard.vue";

export default {
  name: "App",
  components: {
    ScoreBoard,
  },
  data() {
    return {
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswer: undefined,
      chosen_answer: undefined,
      answerSubmitted: false,
      winCount: 0,
      loseCount: 0,
    };
  },
  computed: {
    answers() {
      let answers = JSON.parse(JSON.stringify(this.incorrectAnswers));
      answers.splice(
        Math.round(Math.random() * answers.length),
        0,
        this.correctAnswer
      );
      return answers;
    },
  },
  methods: {
    getNewQuestion() {
      this.chosen_answer = undefined;
      this.answerSubmitted = false;
      this.question = undefined;

      this.axios
        .get("https://opentdb.com/api.php?amount=1")
        .then((response) => {
          this.question = response.data.results[0].question;
          this.incorrectAnswers = response.data.results[0].incorrect_answers;
          this.correctAnswer = response.data.results[0].correct_answer;
        });
    },

    submitAnswer() {
      if (!this.chosen_answer) {
        alert("Pick one of the options");
      } else {
        this.answerSubmitted = true;
        if (this.chosen_answer == this.correctAnswer) {
          this.winCount++;
        } else {
          this.loseCount++;
        }
      }
    },
  },
  created() {
    this.getNewQuestion();
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;
}

h1 {
  margin-top: 40px;
}

input[type="radio"] {
  margin: 12px 4px;
}

button.send {
  margin-top: 12px;
  height: 40px;
  min-width: 120px;
  padding: 0 16px;
  color: #fff;
  background-color: #1867c0;
  border: 1px solid #1867c0;
  cursor: pointer;
}
</style>
