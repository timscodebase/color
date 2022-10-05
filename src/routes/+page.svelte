<script lang="ts">
	const fetchColors = (async () => {
		const response = await fetch('https://api.sampleapis.com/csscolornames/colors');
		return await response.json();
	})();

	const randomColor = async () => {
		const data = await fetchColors;
		const randomIndex: number = Math.floor(Math.random() * data.length);
		return data[randomIndex].hex;
	};

	let isCopied = false;

	const handleClick = async (color: string) => {
		try {
			await navigator.clipboard.writeText(color);
			isCopied = true;
			setTimeout(() => {
				isCopied = false;
			}, 500);
		} catch (err) {
			console.error('Failed to copy!', err);
		}
	};
</script>

{#await randomColor()}
	<div>loading...</div>
{:then color}
	<header style="--backgroundColor: {color}">
		<h1>Colors</h1>
	</header>
{/await}

{#await fetchColors}
	<p>loading...</p>
{:then colors}
	<div class="grid">
		{#each colors as color}
			<div
				class="color"
				data-hover={isCopied ? 'Copied!' : 'Copy'}
				style="--backgroundColor: {color.hex}"
				on:click={() => handleClick(color.hex)}
			>
				<div class="name">{color.name} | {color.hex}</div>
			</div>
		{/each}
	</div>
{:catch error}
	<p>{error.message}</p>
{/await}

<style global>
	html {
		-moz-text-size-adjust: none;
		-webkit-text-size-adjust: none;
		text-size-adjust: none;
	}

	body {
		background: #111;
		font-family: Arial, Helvetica, sans-serif;
	}

	*:where(:not(iframe, canvas, img, svg, video):not(svg *)) {
		all: unset;
		display: revert;
	}

	*,
	*::before,
	*::after {
		box-sizing: border-box;
	}

	ol,
	ul {
		list-style: none;
	}

	img {
		max-width: 100%;
	}

	table {
		border-collapse: collapse;
	}

	textarea {
		white-space: revert;
	}

	:root {
		--padding: max(1rem, 2vw);
	}

	header {
		display: grid;
		place-items: center;
		padding: var(--padding);
		background: var(--backgroundColor);
		color: color-contrast(var(--backgroundColor) vs #222, #eee, rgb(95, 95, 95));
		margin-bottom: var(--padding);
		font-size: clamp(3em, 6vw, 10em);
	}

	.grid {
		display: grid;
		grid-gap: 1em;
		padding: 0 var(--padding);
		grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
	}

	.color {
		position: relative;
		height: 200px;
		background-color: var(--backgroundColor);
		position: relative;
		background: var(--backgroundColor);
	}
	.color:hover {
		cursor: pointer;
	}
	.color:hover::before {
		display: grid;
		place-items: center;
		content: attr(data-hover);
		width: 100%;
		height: 100%;
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background: rgba(0, 0, 0, 0.75);
		color: #fff;
		font-weight: bold;
		font-size: 2em;
	}

	.name {
		position: absolute;
		bottom: 0;
		left: 0;
		padding: 0.5em;
		font-size: 0.7em;
		font-weight: bold;
		text-transform: uppercase;
		color: var(--backgroundColor);
		background: color-contrast(var(--backgroundColor) vs #222, #eee, rgb(95, 95, 95));
	}
</style>
