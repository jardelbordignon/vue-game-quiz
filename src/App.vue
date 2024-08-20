<template>
  <div>
    <template v-if="state.question">
      <ScoreBoard :winCount="state.winCount" :loseCount="state.loseCount" />

      <h1 v-html="state.question"></h1>

      <template v-for="answer in answers" :key="answer">
        <input
          type="radio"
          name="options"
          :disabled="state.answerSubmitted"
          :value="answer"
          v-model="state.chosenAnswer"
        />
        <label v-html="answer"></label><br />
      </template>

      <button
        class="send"
        type="button"
        v-if="!state.answerSubmitted"
        @click="submitAnswer"
      >
        send
      </button>

      <section class="result" v-if="state.answerSubmitted">
        <h4
          v-if="state.correctAnswer === state.chosenAnswer"
          v-html="
            '&#9989; Congratulations, the answer ' +
            state.chosenAnswer +
            ' is correct.'
          "
        ></h4>
        <h4
          v-else
          v-html="
            `&#10060; I',m sorry, the correct answer is ` +
            state.correctAnswer +
            '.'
          "
        ></h4>

        <button class="send" type="button" @click="fetchNewQuestion()">
          Next question
        </button>
      </section>
    </template>
  </div>
</template>

<script lang="ts">
import { Options, Vue } from "vue-class-component";

import ScoreBoard from "./components/ScoreBoard/index.vue";

type QuestionsResponse = {
  response_code: number;
  results: {
    type: string;
    difficulty: "easy" | "medium" | "hard";
    category: string;
    question: string;
    correct_answer: string;
    incorrect_answers: string[];
  }[];
};

type AppState = {
  question: string;
  incorrectAnswers: string[];
  correctAnswer: string;
  chosenAnswer?: string;
  answerSubmitted: boolean;
  winCount: number;
  loseCount: number;
};

@Options({
  components: {
    ScoreBoard,
  },
})
export default class App extends Vue {
  state: AppState = {
    question: "",
    incorrectAnswers: [],
    correctAnswer: "",
    chosenAnswer: undefined,
    answerSubmitted: false,
    winCount: 0,
    loseCount: 0,
  };

  get answers(): string[] {
    const answers = [...this.state.incorrectAnswers];
    const randomPosition = Math.round(Math.random() * answers.length);
    answers.splice(randomPosition, 0, this.state.correctAnswer);
    return answers;
  }

  // methods
  submitAnswer() {
    if (!this.state.chosenAnswer) {
      alert("Pick an answer");
    } else {
      this.state.answerSubmitted = true;
      if (this.state.chosenAnswer === this.state.correctAnswer) {
        this.state.winCount++;
      } else {
        this.state.loseCount++;
      }
    }
  }

  created(): void {
    this.fetchNewQuestion();
  }

  async fetchNewQuestion(): Promise<void> {
    const url = "https://opentdb.com/api.php?amount=1&category=18";
    this.axios
      .get<QuestionsResponse>(url)
      .then(({ data }) => {
        const { question, incorrect_answers, correct_answer } = data.results[0];
        this.state.question = question;
        this.state.incorrectAnswers = incorrect_answers;
        this.state.correctAnswer = correct_answer;
        this.state.answerSubmitted = false;
        this.state.chosenAnswer = undefined;
      })
      .catch((error) => {
        console.log(error);
      });
  }
}
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

  > div {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 30px;
    background-color: #f5f5f5;
  }

  input[type="radio"] {
    margin: 12px 4px;
  }

  button.send {
    margin-top: 12px;
    padding: 12px 24px;
    font-size: 16px;
    font-weight: bold;
    border: none;
    border-radius: 8px;
    background-color: #111;
    color: white;
  }
}
</style>
