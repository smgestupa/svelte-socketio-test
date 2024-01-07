<script>
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';
	import { socket } from '$lib/socket.js';
	import { current_user } from '$lib/store.js';

	let messages = [];

	const sendMessage = (event) => {
		const formData = new FormData(event.target);

		const message = formData.get('message').trim();

		if (!message) return;

		socket.emit('new_message', message);
		event.target[0].value = '';
	};

	socket.on('chat', (data) => {
		const { username, message } = data;
		messages = [{ username: username, message }, ...messages];
	});

	onMount(() => {
		if (!$current_user) goto('/', { replaceState: true });
	});
</script>

<div class="flex w-full flex-grow flex-col p-10">
	<section
		class="focus:shadow-outline m-auto flex h-0 w-10/12 flex-grow appearance-none flex-col gap-y-2 overflow-y-auto rounded border bg-white p-3 shadow focus:outline-none"
	>
		{#each messages as { username, message }}
			<div class="rounded p-2" class:bg-gray-300={username == $current_user}>
				<p>
					<span class="mr-2 font-bold" class:bg-gray-300={username == current_user}>{username}</span
					>{message}
				</p>
			</div>
		{/each}
	</section>
	<form
		class="focus:shadow-outline mx-auto mt-2 flex h-fit w-10/12 appearance-none gap-x-2 rounded border bg-white px-1 py-1 shadow focus:outline-none"
		on:submit|preventDefault={sendMessage}
	>
		<input
			class="w-full py-3 pl-2 focus:outline-none"
			type="text"
			name="message"
			placeholder="Type your message here"
		/>
		<button
			class="block rounded border-b-4 border-blue-700 bg-blue-500 px-4 py-2 font-bold text-white hover:border-blue-500 hover:bg-blue-400"
			type="submit"
		>
			<svg
				xmlns="http://www.w3.org/2000/svg"
				width="24"
				height="24"
				viewBox="0 0 24 24"
				fill="none"
				stroke="currentColor"
				stroke-width="2"
				stroke-linecap="round"
				stroke-linejoin="round"
				><line x1="22" y1="2" x2="11" y2="13"></line><polygon points="22 2 15 22 11 13 2 9 22 2"
				></polygon></svg
			>
		</button>
	</form>
</div>
