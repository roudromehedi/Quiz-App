<script setup>
import { ref, reactive } from "vue";

const questions = reactive([
  {
    question: "What is 5 + 2?",
    answers: [
      { text: "4", is_correct: false },
      { text: "3", is_correct: false },
      { text: "7", is_correct: true },
      { text: "5", is_correct: false },
    ],
  },
  {
    question: 'How many letters are in the word "Banana"?',
    answers: [
      { text: "5", is_correct: false },
      { text: "7", is_correct: false },
      { text: "6", is_correct: true },
      { text: "12", is_correct: false },
    ],
  },
  {
    question: "Find the missing letter: C_ke",
    answers: [
      { text: "e", is_correct: false },
      { text: "a", is_correct: true },
      { text: "i", is_correct: false },
      { text: "p", is_correct: false },
    ],
  },
]);

const answeredQuestions = reactive([]);

const selectAnswer = (selectedAnswer) => {
  const answeredIndex = answeredQuestions.findIndex(
    (q) => q.question === currentQuestion.value.question
  );

  if (answeredIndex !== -1) {
    answeredQuestions[answeredIndex].selectedAnswer = selectedAnswer;
  } else {
    answeredQuestions.push({
      question: currentQuestion.value.question,
      selectedAnswer,
    });
  }
};

const currentQuestion = ref(questions[0]);
let currentIndex = 0;

const changeQuestion = (navigation) => {
  if (navigation === "next") {
    currentIndex = Math.min(currentIndex + 1, questions.length - 1);
  } else if (navigation === "previous") {
    currentIndex = Math.max(currentIndex - 1, 0);
  }

  currentQuestion.value = questions[currentIndex];
};

const showResult = ref(false);
let result = 0;

const calculateResult = () => {
  result = answeredQuestions.reduce(
    (count, answer) => count + (answer.selectedAnswer ? 1 : 0),
    0
  );
  showResult.value = true;
  return result;
};
</script>

<template>
  <div class="container mx-auto py-8">
    <div class="bg-white p-8 rounded shadow-lg">
      <div v-if="!showResult">
        <!-- Progress bar -->
        <div class="w-full mb-4">
          <div class="bg-gray-300 h-4 rounded">
            <div
              class="bg-blue-500 h-full rounded"
              :style="{
                width:
                  (answeredQuestions.length / questions.length) * 100 + '%',
              }"
            ></div>
          </div>
        </div>

        <!-- Question -->
        <div class="text-lg font-semibold mb-4">
          {{ currentQuestion.question }}
        </div>

        <!-- Multiple-choice answers -->
        <div class="space-y-2">
          <label
            v-for="answer in currentQuestion.answers"
            :key="answer.text"
            class="flex items-center"
          >
            <input
              type="radio"
              name="answer"
              class="form-radio"
              :value="answer.text"
              @change="selectAnswer(answer.is_correct)"
            />
            <span class="ml-2">{{ answer.text }}</span>
          </label>
        </div>

        <!-- Previous button -->
        <button
          @click="changeQuestion('previous')"
          class="mt-4 px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
        >
          Previous
        </button>

        <!-- Next button -->
        <button
          v-show="answeredQuestions.length < questions.length"
          @click="changeQuestion('next')"
          class="ml-5 mt-4 px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
        >
          Next
        </button>

        <!-- Submit button -->
        <button
          v-if="answeredQuestions.length === questions.length"
          @click="calculateResult()"
          class="ml-5 mt-4 px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
        >
          Submit
        </button>
      </div>

      <!-- Show result -->
      <div v-if="showResult" class="text-lg font-semibold mb-4">
        Your result: {{ calculateResult() }} out of {{ questions.length }} is
        correct!
      </div>
    </div>
  </div>
</template>

<style scoped></style>
