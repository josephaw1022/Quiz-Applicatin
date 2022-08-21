<script lang="ts">
import { defineComponent } from 'vue';
import { QuestionList } from './constants/Questions';
import type { IQuestion } from './constants/Questions';

/**
 * The structure for the user's answers.
 */
type IUserAnswer = {
	question: string;
	answer: string;
};

/**
 * The structure of the data from the data method below.
 */
type IDataType = {
	questions: IQuestion[];
	questionNumber: number;
	userAnswers: IUserAnswer[];
	temporarySelection: string;
};

export default defineComponent({
	data(): IDataType {
		return {
			questions: QuestionList,
			questionNumber: 0,
			userAnswers: [],
			temporarySelection: '',
		};
	},
	methods: {
		/**
		 * handleAnswerSelection - The method that is called when the user selects an answer.
		 */
		handleAnswerSelection: function (questionAnswer: string): void {
			this.temporarySelection = questionAnswer;
		},

		/**
		 * handleQuestionSubmission - Handles the logic of finishing a question.
		 */
		handleQuestionSubmission: function (): void {
			// Get the user's answer.
			const userAnswer = this.temporarySelection;

			// If the user did not answer, then return early.
			if (!userAnswer) {
				return;
			}

			// Get the current question.
			const currentQuestion = this.questions[this.questionNumber];

			// Add the user's answer to the user's answers array.
			this.userAnswers.push({
				question: currentQuestion.text,
				answer: userAnswer,
			});

			// Increment the question number.
			this.questionNumber++;

			// Clear the temporary selection.
			this.temporarySelection = '';
		},

	
		/**
		 * handleRestartQuiz - Handles the logic of restarting the quiz.
		 */
		handleRestartQuiz: function (): void {
			this.questionNumber = 0;
			this.userAnswers = [];
		},
	},
});
</script>

<template>
	<div
		class="flex flex-col items-center justify-center py-10 h-screen"
	>
		<section class="h-9/12 w-9/12 flex-1 flex flex-col">
			<header
				class="duration-150 px-2 sm:px-4 md:px-8 lg:px-10 justify-between flex bg-blue-500 h-20 md:h-24 lg:h-36 items-center rounded-lg"
			>
				<h1
					class="text-xl text-white md:text-3xl lg:text-4xl 2xl:text-5xl duration-150"
					v-if="Boolean(questions[questionNumber])"
				>
					Question {{ questionNumber + 1 }} of {{ questions.length }}
				</h1>

				<!-- The heading for when done with the quiz -->
				<h1
					class="text-xl text-white md:text-3xl lg:text-4xl 2xl:text-5xl duration-150"
					v-if="!Boolean(questions[questionNumber])"
				>
					Results
				</h1>

				<!-- The button to redo the quiz -->
				<button
					class="bg-white px-2 rounded-lg text-black text-xl h-12 disabled:text-gray-400 disabled:bg-gray-200 disabled:cursor-not-allowed"
					@click="handleRestartQuiz"
					v-if="!Boolean(questions[questionNumber])"
				>
					Redo Quiz
				</button>
			</header>

			<!-- The quiz part of the application -->
			<main
				v-if="Boolean(questions[questionNumber])"
				class="px-2 py-2 flex-1 flex flex-col h-full"
			>
				<div
					class="flex flex-row justify-start items-center h-24 md:h-28 lg:h-32 mb-5 md:mb-1"
				>
					<h1
						class="text-md sm:text-lg md:text-xl lg:text-2xl bg-white text-blue-500 rounded-lg px-6 border-2 py-1 lg:py-4 select-none"
					>
						Question: {{ questions[questionNumber].text }}
					</h1>
				</div>
				<div class="flex-col flex gap-10 flex-1">
					<div
						class="duration-150 bg-white border-2 flex flex-row justify-start items-center h-20 px-4 rounded-md hover:shadow-lg"
						v-for="questionAnswer in questions[questionNumber]
							.answers"
						@click="() => handleAnswerSelection(questionAnswer)"
					>
						<input
							type="checkbox"
							name="answer"
							class="form-check-input appearance-none h-3 w-3 md:h-4 md:w-4 lg:h-6 lg:w-6 border border-gray-300 rounded-sm bg-white checked:bg-blue-300 checked:border-blue-600 focus:outline-none transition duration-200 bg-no-repeat bg-center bg-contain cursor-pointer"
							:value="questionAnswer"
							:checked="temporarySelection === questionAnswer"
						/>
						<label for="answer" class="text-black px-4 text-lg">
							{{ questionAnswer }}
						</label>
					</div>
				</div>
			</main>

			<!-- The summary of the quiz answers -->
			<main
				v-if="!Boolean(questions[questionNumber])"
				class="px-2 py-2 flex-1 flex flex-col h-full select-none"
			>
				<div class="flex-col flex gap-10 flex-1 mt-4">
					<div
						class="duration-150 bg-white border-2 flex flex-row justify-between items-center px-4 rounded-md md:min-h-24"
						v-for="userAnswer in userAnswers"
					>
						<div class="flex-1 flex flex-col gap-4">
							<h1 class="text-md md:text-lg lg:text-2xl">
								Question: {{ userAnswer.question }}
							</h1>
							<h2 class="text-sm md:text-md lg:text-xl">
								Answer: {{ userAnswer.answer }}
							</h2>
						</div>
					</div>
				</div>
			</main>

			<!-- The footer of the quiz -->
			<footer
				v-if="Boolean(questions[questionNumber])"
				class="bg-blue-500 flex flex-row justify-center md:justify-end items-center h-24 duration-150 rounded-lg px-8"
			>
				<button
					:disabled="!Boolean(temporarySelection)"
					class="bg-white px-2 rounded-lg text-black text-xl h-12 disabled:text-gray-400 disabled:bg-gray-200 disabled:cursor-not-allowed flex-1 md:flex-none md:w-36"
					@click="handleQuestionSubmission"
				>
					{{
						questionNumber === questions.length - 1
							? 'Finish Quiz'
							: 'Next'
					}}
				</button>
			</footer>
		</section>
	</div>
</template>
