<script lang="ts">
	import type { Todo } from "./@types";
	import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher();

	import { crossfade } from 'svelte/transition';
	import { quintOut } from 'svelte/easing';

	const [send, receive] = crossfade({
		duration: d => Math.sqrt(d * 200),

		fallback(node, params) {
			const style = getComputedStyle(node);
			const transform = style.transform === 'none' ? '' : style.transform;

			return {
				duration: 600,
				easing: quintOut,
				css: t => `
					transform: ${transform} scale(${t});
					opacity: ${t}
				`
			};
		}
	});
	
	export let todo: Todo;

	function toggle(todo) {
		dispatch(('toggle'), todo);
	}

	function remove(todo) {
		dispatch(('remove'), todo);
	}
</script>

<label class="label" class:done={todo.done} in:receive={{key: todo.id}} out:send={{key: todo.id}}>
	<input class="checkbox" checked={todo.done} type="checkbox" on:change={() => toggle(todo)} />
	{todo.todo}
	<button class="remove" on:click={() => remove(todo)}>Remove</button>
</label>

<style>
	.label {
		position: relative;
		line-height: 1.2;
		padding: 0.5rem 2.5rem 0.5rem 2rem;
		margin-bottom: 0.5rem;
		border-radius: 2px;
		user-select: none;
		border: 1px solid hsl(240, 8%, 70%);
		background-color:hsl(240, 8%, 93%);
		color: #333;
		cursor: pointer;
		text-align: left;
	}

	.label.done {
		border: 1px solid hsl(240, 8%, 70%);
		background-color:rgb(248, 248, 248);
	}

	.remove {
		position: absolute;
		top: 0;
		right: 0.2em;
		width: 2em;
		height: 100%;
		background: no-repeat 50% 50% url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24'%3E%3Cpath fill='%23676778' d='M12,2C17.53,2 22,6.47 22,12C22,17.53 17.53,22 12,22C6.47,22 2,17.53 2,12C2,6.47 6.47,2 12,2M17,7H14.5L13.5,6H10.5L9.5,7H7V9H17V7M9,18H15A1,1 0 0,0 16,17V10H8V17A1,1 0 0,0 9,18Z'%3E%3C/path%3E%3C/svg%3E");
		background-size: 1.4em 1.4em;
		border: none;
		opacity: 0;
		transition: opacity 0.1s ease-in;
		text-indent: -9999px;
		cursor: pointer;
	}

	.label:hover .remove {
		cursor: pointer;
		opacity: 1;
	}

	.checkbox {
		position: absolute;
		left: 0.5rem;
		top: 0.6rem;
		margin: 0;
	}
</style>