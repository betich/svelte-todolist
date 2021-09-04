<script lang="ts">
	import { onMount } from "svelte";
	import type { Todo } from "./@types";
	import List from "./List.svelte";
	import { flip } from 'svelte/animate';

	let id = 0;
	let todos: Todo[] = [
		{id: id++, todo: "Try Framer Motion", done: false},
		{id: id++, todo: "Try React Spring", done: false},
		{id: id++, todo: "Learn SvelteKit and other Svelte stuff", done: false},
		{id: id++, todo: "Learn Koa.js", done: false},
		{id: id++, todo: "Learn NestJS", done: false},
		{id: id++, todo: "Dominate the world", done: false},
		{id: id++, todo: "Learn Basic Svelte", done: true},
		{id: id++, todo: "ซุก", done: true}
	];

	let ref: null | HTMLInputElement = null;

	onMount(() => {
		ref.focus();
	})

	function add() {
		if (!ref.value) return;
		todos = [{id: id++, todo: ref.value, done: false}, ...todos];
		ref.value = '';
	}

	function remove(event) {
		let todo = event.detail;
		todos = todos.filter(t => t !== todo);
	}

	function toggle(event) {
		let todo = event.detail;
		remove(event); // remove the checked todo first
		todos = todos.concat({ todo: todo.todo, done: !todo.done, id: id++ }); // then reassign it back
	}
</script>

<main>
	<header class="header">
		<h1>Svelte Todo List</h1>
		<input type="text" class="input" on:keydown={e => e.key === 'Enter' && add()} bind:this={ref} />
		<button class="add" on:click={add}>Add Todo</button>
	</header>

	<div class="todo-container">
		<div class="left">
			<h2>Todos</h2>
			{#each todos.filter(t => !t.done) as todo (todo.id)}
				<div animate:flip={{duration: 200}}>
					<List todo={todo} on:toggle={toggle} on:remove={remove}/>
				</div>
			{/each}
		</div>
		<div class="right">
			<h2>Done</h2>
			{#each todos.filter(t => t.done) as todo (todo.id)}
				<div animate:flip={{duration: 200}}>
					<List todo={todo} on:toggle={toggle} on:remove={remove}/>
				</div>
			{/each}
		</div>
	</div>
</main>

<style>
	:root {
		font-size: 16px;
	}

	button {
		cursor: pointer;
		transition: filter .3s ease;
	}

	.todo-container {
		display: grid;
		grid-template-columns: 1fr 1fr;
		grid-gap: 1em;
		margin: 0 auto;
		padding: 0 12px;
	}

	.left {
		margin-right: 24px;
	}

	button:hover {
		filter: brightness(0.9);
	}

	main {
		text-align: center;
		margin: 0;
	}

	.header {
		margin-bottom: 32px;
	}

	.input {
		padding: 12px 24px;
		min-width: 80%;
		height: 75px;
		font-size: 1.5rem;
	}

	.add {
		padding: 12px 24px;
		display: block;
		margin: 0 auto;
		background-color: #ff3e00;
		color: #fff;
	}

	h1 {
		color: #ff3e00;
		font-size: 4em;
		font-weight: 100;
		text-align: center;
		width: 100%;
	}

	h2 {
		color: #ff3e00;
		font-size: 2em;
		font-weight: 100;
		text-align: left;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
			padding: 1rem;
			margin: 0 auto;
		}

		.input {
			min-width: 95%;
		}
	}
</style>
