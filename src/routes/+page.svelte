<h1>Welcome to SvelteKit</h1>

<button
    on:click={async () => {
		const { fooInstance } = (await import("$lib/worker/foo-instance"));
		fooInstance.postMessage(Math.random());
    }}
>test worker</button>