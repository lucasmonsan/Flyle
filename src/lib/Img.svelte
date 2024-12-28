<script lang="ts">
	import { fade } from 'svelte/transition'

	export let src: string
	export let alt: string
	export let loading: 'lazy' | 'eager' = 'lazy'
	export let width: string | undefined = 'auto'
	export let height: string | undefined = '100%'
	export let decoding: 'sync' | 'async' | 'auto' = 'auto'

	let loaded = false
	let error = false

	async function fetchImage() {
		try {
			const img = new Image()
			img.src = src
			await img.decode()
			loaded = true
		} catch (e) {
			error = true
		}
	}

	$: {
		if (src) {
			error = false
			loaded = false
			fetchImage()
		}
	}
</script>

<img {src} {alt} {loading} {width} {height} {decoding} {...$$restProps} class={loaded && !error ? 'show' : ''} />

{#if error}
	<slot name="fallback">
		<p transition:fade>Erro ao carregar imagem</p>
	</slot>
{/if}

<style>
	img {
		height: 100%;
		width: 100%;
		object-fit: cover;
		opacity: 0;
		filter: blur(1rem);
		transition:
			opacity 1s ease-in-out,
			filter 2s ease-in-out;
	}

	.show {
		opacity: 1;
		filter: blur(0);
	}
</style>
