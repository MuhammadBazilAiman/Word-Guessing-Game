<script setup>
import { ref } from "vue";

let randomWord = ref();
let randomWordArray = ref();
let wordUnderscore = ref([]);
let randomWordHint = ref();
let correctLetters = ref([]);
let correctLettersStatus = ref(false);
let correctLettersSorted = ref();
let numberOfTry = ref(5);
let randomWordLength = ref();
let answer = ref("");
let winner = ref(false);
let lose = ref(false);
let endGame = ref(false);
let repeatedAnswer = ref(false);
let isWrongAnswer = ref(false);

const wordLists = ref([
  {
    word: "cat",
    hint: "well known animal",
  },
  {
    word: "apple",
    hint: "well known fruit",
  },
  {
    word: "sleep",
    hint: "everyone's favourite activity",
  },
  {
    word: "music",
    hint: "stuff everyone listens to",
  },
  {
    word: "guitar",
    hint: "typical music instrument",
  },
]);

// generate random word
const generateRandomWord = () => {
  randomWord.value =
    wordLists.value[Math.floor(Math.random() * wordLists.value.length)];
  randomWordHint.value = randomWord.value.hint;
  randomWordArray.value = randomWord.value.word.split("");
  randomWordLength.value = randomWord.value.word.length;
};

// generate underscore for the random word
const generateWordUnderscore = () => {
  generateRandomWord();
  while (wordUnderscore.value.length < randomWord.value.word.length) {
    wordUnderscore.value.push("_");
  }
};

// ask for user's guess
const checkLetter = () => {
  repeatedAnswer.value = false;
  isWrongAnswer.value = false;

  // for correct letter
  for (let i = 0; i < randomWordArray.value.length; i++) {
    if (answer.value === randomWordArray.value[i]) {
      // if same answer
      if (wordUnderscore.value[i] === answer.value) {
        correctLettersStatus.value = true;
        repeatedAnswer.value = true;
        break;
      }
      wordUnderscore.value[i] = answer.value;
      correctLetters.value.push(answer.value);
      correctLettersSorted.value = correctLetters.value.sort();
      correctLettersStatus.value = true;
      // if got winner
      if (correctLetters.value.length === randomWordArray.value.length) {
        winner.value = true;
        endGame.value = true;
        break;
      }
    }
  }

  // for wrong letters, minus numberOfTry
  if (!correctLettersStatus.value) {
    isWrongAnswer.value = true;
    numberOfTry.value--;
    if (numberOfTry.value === 0) {
      lose.value = true
      endGame.value = true
    }
  }

  correctLettersStatus.value = false;
  answer.value = "";

};

const resetAnimation = () => {
  return isWrongAnswer.value = false
}

</script>

<template>
  <main id="app">
    <h1>Guess Me If You Can 🧐</h1>

    <div class="main-box">

      <div class="generate-box">
        <button @click="generateWordUnderscore()">Generate Word</button>
      </div>

      <div class="underscores">
        <p class="underscores-text">{{ wordUnderscore.toString().replaceAll(",", " ") }}</p>
        <p class="underscores-hint">Hint: {{ randomWordHint }}</p>
      </div>

      <div class="number-of-try">
        <p :class="{active: isWrongAnswer}">Number of try: {{ numberOfTry }}</p>
      </div>

      <div class="buttons">
        <form @submit.prevent="checkLetter">
          <input @change="resetAnimation" v-if="!endGame" placeholder="Enter a letter" type="text" v-model="answer" />
        </form>

        <div class="display">
          <p v-if="winner">Congrats, you guessed it right! 🎉</p>
          <p v-else-if="lose">Out of try 😯</p>
          <p v-if="repeatedAnswer">Oops, you have inputted that letter. How about trying other letter ? 😉</p>
        </div>
      </div>

      <div class="correct-letters-box">
        <p class="correct-letters-words">Correct letters: {{ correctLettersSorted }}</p>
      </div>

    </div>
    <footer>
      <p>by Muhammad Bazil Aiman 🌹</p>
    </footer>
  </main>
</template>
