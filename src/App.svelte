<script async script lang="ts">
	import Question from "./lib/Question.svelte";
	import questions from "./lib/Questions.json";

	let questionArr = questions.slice();

	let searchTerm: string;

	$: {
		if (searchTerm || searchTerm == "") {
			let newQuestions = [];

			for (let i = 0; i < questions.length; i++) {
				const question = questions[i];
				if (
					question.question
						.toLowerCase()
						.includes(searchTerm.toLowerCase())
				) {
					newQuestions.push(question);
				}
			}

			questionArr = newQuestions;
		}
	}
</script>

<main>
	<h1>1IMA FAQ</h1>

	<br />

	<input id="searchBox" bind:value={searchTerm} type="text" />

	<hr />

	{#each questionArr as question}
		<Question
			data={{ question: question.question, answer: question.answer }}
		/>
	{/each}
</main>

<style>
	#searchBox {
		font-size: 20px;
		max-width: 45em;
		width: 100%;
		padding: 0.5em;
	}

	main {
		width: 100%;
	}

	hr {
		margin-top: 1em;
		margin-bottom: 1em;
	}
</style>
