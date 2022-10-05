<script async script lang="ts">
	import Question from "./lib/Question.svelte";
	import questions from "./lib/Questions.json";

	let questionArr = questions.slice();

	let tags: string[] = [];
	questionArr.forEach((question) => {
		const re = RegExp(/[^.] ([A-ZÆØÅ][^ \.?",\n:]{2,})/gm);

		let m: RegExpExecArray;

		let q = question.question.slice();
		do {
			m = re.exec(q);
			if (m) {
				if (!tags.includes(m[1])) tags.push(m[1]);
			}
		} while (m);

		q = question.answer.slice();
		do {
			m = re.exec(q);
			if (m) {
				if (!tags.includes(m[1])) tags.push(m[1]);
			}
		} while (m);
	});

	tags.sort((a, b) => {
		return a.localeCompare(b);
	});

	let searchTerm: string;

	$: {
		if (searchTerm || searchTerm == "") {
			let newQuestions = [];

			for (let i = 0; i < questions.length; i++) {
				const question = questions[i];

				let includes = true;
				for (let x = 0; x < searchTerm.split(" ").length; x++) {
					const term = searchTerm.split(" ")[x];

					if (
						!(question.question + ". " + question.answer)
							.toLowerCase()
							.includes(term.toLowerCase())
					) {
						includes = false;
					}
				}
				if (includes) {
					newQuestions.push(question);
				}
			}

			questionArr = newQuestions;
		}
	}

	let selectedTags: HTMLElement[] = [];

	function tagClick(
		ev: MouseEvent & {
			currentTarget: EventTarget & HTMLHeadingElement;
		},
		tag: string
	) {
		if (!searchTerm) searchTerm = "";

		if (selectedTags.includes(ev.target as HTMLElement)) {
			searchTerm = searchTerm.replace(tag, "").trim();
		} else {
			searchTerm += " " + tag;
			searchTerm = searchTerm.trim();
		}

		if ((ev.target as HTMLElement).className.includes("selected")) {
			(ev.target as HTMLElement).className = (
				ev.target as HTMLElement
			).className.replace(" selected", "");

			selectedTags.splice(
				selectedTags.indexOf(ev.target as HTMLElement),
				1
			);
		} else {
			(ev.target as HTMLElement).className += " selected";
			selectedTags.push(ev.target as HTMLElement);
		}
	}
</script>

<main>
	<h1>1IMA FAQ</h1>

	<br />

	<input
		id="searchBox"
		bind:value={searchTerm}
		on:keypress={() => {
			selectedTags.forEach(
				(el) => (el.className = el.className.replace(" selected", ""))
			);

			selectedTags = [];
		}}
		type="text"
	/>

	<div id="tags">
		{#each tags as tag}
			<h5
				on:click={(ev) => {
					tagClick(ev, tag);
				}}
			>
				#{tag}
			</h5>
		{/each}
	</div>

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
	#tags {
		display: flex;
		justify-content: center;
		flex-direction: row;
		flex-wrap: wrap;
	}

	#tags > h5 {
		border: solid 2px #f5f5f5;
		width: max-content;
		padding: 0.5em;
		transition-duration: 0.3s;
		margin: 0 0.5em;
		margin-top: 1em;
	}
	#tags > h5:hover {
		background-color: #ccc;
		color: #333;
		cursor: pointer;
	}
</style>
