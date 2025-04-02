<script lang="ts">
	import { onMount } from 'svelte';

	let text = '';
	let ws: WebSocket;
	let timeoutId: any = null;

	onMount(() => {
		ws = new WebSocket('ws://collabrative-core-backend.kmeetz016.workers.dev/websocket');
		ws.onopen = () => console.log('WebSocket connection established');
		ws.onmessage = (event) => {
			const data = JSON.parse(event.data);
			if (data.type === 'text') {
				text = data.content;
			}
		};
		ws.onclose = () => console.log('WebSocket connection closed');
	});

	function handleUpdate(event: Event) {
		const target = event.target as HTMLTextAreaElement;
		text = target.value;
		clearTimeout(timeoutId);
		timeoutId = setTimeout(() => {
			if (ws.readyState === WebSocket.OPEN) {
				ws.send(JSON.stringify({ type: 'text', content: text }));
			}
		}, 500);
	}
</script>

<div class="flex h-screen w-full items-center justify-center p-8">
	<textarea
		bind:value={text}
		on:input={handleUpdate}
		placeholder="Start typing..."
		class="h-[80vh] w-full max-w-4xl resize-none rounded-lg border border-gray-300 bg-white p-4 font-sans text-base leading-relaxed shadow-sm focus:border-transparent focus:ring-2 focus:ring-blue-500 focus:outline-none"
	></textarea>
</div>
