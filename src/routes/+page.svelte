<script>
	import { goto } from '$app/navigation';
	import { socket } from '$lib/socket.js';
	import { current_user } from '$lib/store.js';

	const joinChatroom = (event) => {
		const formData = new FormData(event.target);
		const username = formData.get('username').trim();

		if (!username) return;

		socket.connect();
		socket.on('connect', () => {
			socket.emit('user_join', username);
			$current_user = username;
			goto('/chat');
		});
	};
</script>

<form class="m-auto" on:submit|preventDefault={joinChatroom}>
	<input
		class="focus:shadow-outline w-60 appearance-none rounded border px-3 py-2 leading-tight text-gray-700 shadow focus:outline-none"
		name="username"
		type="text"
		placeholder="Username"
	/>
	<button
		type="submit"
		class="mx-auto mt-3 block rounded border-b-4 border-blue-700 bg-blue-500 px-8 py-2 font-bold text-white hover:border-blue-500 hover:bg-blue-400"
		>Join</button
	>
</form>
