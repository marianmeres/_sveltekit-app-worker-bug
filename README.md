# sveltekit build bug (worker related)

This is a template app built with `npm create svelte@latest`, with:

```javascript
"devDependencies": {
    "@sveltejs/adapter-auto": "^2.0.0",
    "@sveltejs/kit": "^1.5.0",
    "svelte": "^3.56.0",
    "svelte-check": "^3.0.1",
    "tslib": "^2.5.0",
    "typescript": "^5.0.0",
    "vite": "^4.2.0"
}
```

`npm run dev` works as expected

`npm run build` shows (maybe innocent) `The emitted file "vite-manifest.json" overwrites a previously emitted file of the same name.` 
and **eventually hangs** after success message `✓ built in Xms`.

## Gist

```javascript
// $lib/worker/foo.ts
self.addEventListener('message', (message) => console.log(message.data));

// $lib/worker/foo-instance.ts
export const fooInstance = new Worker(new URL('./foo', import.meta.url));

// +page.svelte
<button on:click={async () => {
  const { fooInstance } = (await import("$lib/worker/foo-instance"));
  fooInstance.postMessage(Math.random());
}}>test worker</button>

```

## Full build log
```bash
$ npm run build

> my-sveltekit-app@0.0.1 build
> vite build


vite v4.2.1 building SSR bundle for production...
✓ 49 modules transformed.
10:22:07 AM [vite-plugin-svelte] ssr compile done.
package         	files	  time	  avg
my-sveltekit-app	    2	16.8ms	8.4ms
@sveltejs/kit   	    2	14.5ms	7.2ms

vite v4.2.1 building for production...
✓ 41 modules transformed.
10:22:07 AM [vite-plugin-svelte] dom compile done.
package         	files	  time	   avg
my-sveltekit-app	    2	24.8ms	12.4ms
@sveltejs/kit   	    2	12.5ms	 6.2ms
rendering chunks (12)...The emitted file "vite-manifest.json" overwrites a previously emitted file of the same name.
.svelte-kit/output/client/_app/version.json                                  0.03 kB
.svelte-kit/output/client/_app/immutable/workers/foo-cef06084.js             0.09 kB
.svelte-kit/output/client/vite-manifest.json                                 0.15 kB
.svelte-kit/output/client/_app/immutable/chunks/2.8f813db6.js                0.08 kB │ gzip: 0.10 kB
.svelte-kit/output/client/_app/immutable/chunks/1.048099e4.js                0.08 kB │ gzip: 0.10 kB
.svelte-kit/output/client/_app/immutable/chunks/0.96ee9cf1.js                0.09 kB │ gzip: 0.10 kB
.svelte-kit/output/client/_app/immutable/chunks/foo-instance.9b9c81d5.js     0.13 kB │ gzip: 0.13 kB
.svelte-kit/output/client/_app/immutable/entry/layout.svelte.395df68e.js     0.54 kB │ gzip: 0.36 kB
.svelte-kit/output/client/_app/immutable/chunks/preload-helper.41c905a7.js   0.76 kB │ gzip: 0.48 kB
.svelte-kit/output/client/_app/immutable/entry/_page.svelte.f4cc5663.js      0.84 kB │ gzip: 0.54 kB
.svelte-kit/output/client/_app/immutable/entry/error.svelte.ef67f5ef.js      0.98 kB │ gzip: 0.57 kB
.svelte-kit/output/client/_app/immutable/chunks/singletons.dadda3d5.js       2.81 kB │ gzip: 1.44 kB
.svelte-kit/output/client/_app/immutable/entry/app.055a5469.js               4.94 kB │ gzip: 1.86 kB
.svelte-kit/output/client/_app/immutable/chunks/index.b2ee0b6c.js            7.16 kB │ gzip: 2.89 kB
.svelte-kit/output/client/_app/immutable/entry/start.b4dfaf13.js            23.31 kB │ gzip: 9.27 kB
✓ built in 243ms
```