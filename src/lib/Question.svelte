<script lang="ts">
	export let data: { question: string; answer: string };
</script>

<div id="question">
	<h3>{data.question}</h3>
	<p>{data.answer}</p>
</div>

<style>
	#question {
		border: solid;
		border-radius: 1em;
		margin-bottom: 1em;
		padding: 0 1em;
	}
</style>
