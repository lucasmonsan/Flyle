<script lang="ts">
	import { fade } from 'svelte/transition'

	export let src: string
	export let alt: string
	export let loading: 'lazy' | 'eager' = 'lazy'

	let loaded = false
	let error = false

	async function fetchImage() {
		try {
			const response = await fetch(src)
			if (!response.body) throw new Error('Não foi possível ler a imagem')
			const reader = response.body.getReader()

			while (true) {
				const { done } = await reader.read()
				if (done) break
			}

			loaded = true
		} catch (e) {
			error = true
		}
	}

	$: if (src) fetchImage()
</script>

<img {src} {alt} {loading} {...$$restProps} class={loaded && !error ? 'show' : ''} />

{#if error}
	<p transition:fade>Erro ao carregar imagem</p>
{/if}

<style>
	img {
		height: 100%;
		opacity: 0;
		filter: blur(1rem);
		transition:
			opacity 1s ease-in-out,
			filter 2s ease-in-out;

		&.show {
			opacity: 1;
			filter: blur(0);
		}
	}
</style>
