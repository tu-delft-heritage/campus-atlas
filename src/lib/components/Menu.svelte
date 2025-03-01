<script lang="ts">
	import { slideData } from '$lib/shared/stores/markdownSlides.js'
	import { menu, bear } from '$lib/shared/stores/componentStates.js'
	import { fade } from 'svelte/transition'

	const toggleMenu = () => menu.toggle()

	// Based on eyeball animation: https://codepen.io/GabEsu/pen/VdKjPE

	let marker: SVGCircleElement | undefined
	let markerRect: DOMRect | undefined
	let R: number | undefined
	let centerX: number | undefined
	let centerY: number | undefined
	let angle: number
	let hover: boolean = false
	$: transform = `rotate(${angle ? angle : 0})`
	$: transformInv = `rotate(${angle ? 360 - angle : 0})`

	function setMarker(event: MouseEvent) {
		if (marker) {
			const x = event.clientX - centerX
			const y = event.clientY - centerY
			const theta = Math.atan2(y, x)
			angle = (theta * 180) / Math.PI + 360
		}
	}

	$: {
		if (marker) {
			markerRect = marker.getBoundingClientRect()
			R = markerRect.width / 2
			centerX = markerRect.left + R
			centerY = markerRect.top + R
		}
	}
</script>

<svelte:window on:mousemove={(event) => setMarker(event)} />

<div class="menu" transition:fade={{ duration: 600 }}>
	<div class="container">
		<div class="menu-items">
			<ul class="chapters">
				{#each [...$slideData.keys()] as chapter}
					<li>
						<a on:click={toggleMenu} on:click={() => (hover = true)} href="#/{chapter}"
							>{chapter.replace('-', ' ')}</a
						>
					</li>
					<ul class="slideshows">
						{#each [...$slideData.get(chapter).keys()].slice(1) as slideshow}
							<li>
								<a
									on:click={toggleMenu}
									on:click={() => (hover = true)}
									href="#/{chapter}/{slideshow}/1"
									>{slideshow.charAt(0).toUpperCase() + slideshow.slice(1).replaceAll('-', ' ')}</a
								>
							</li>
						{/each}
					</ul>
				{/each}
			</ul>
		</div>
	</div>
</div>

<style>
	.menu {
		grid-column: 1 / 5;
		grid-row: 1 / 3;
		background-color: rgba(0, 0, 0, 0.7);
		padding: 3rem 1rem 1rem 1rem;
		min-width: 0;
		min-height: 0;
		overflow: auto;
		z-index: 5;
		display: flex;
		line-height: 1.3;
		color: white;
	}
	.container {
		display: flex;
		flex-wrap: wrap;
		width: 100%;
		height: 100%;
	}
	a {
		color: white;
		text-decoration: none;
	}
	a:hover {
		color: rgba(255, 255, 114);
		text-decoration: none;
	}
	.logo {
		order: 1;
		height: 100%;
		width: 50%;
		& svg {
			width: 100%;
			height: 100%;
		}
	}
	.menu-items {
		order: 2;
		display: flex;
		justify-content: center;
		align-items: center;
		height: 100%;
		width: 100%;
		margin: 0;
		font-size: 1.2rem;
		text-transform: capitalize;
	}
	.circle {
		transform-origin: 127px 64px;
	}
	@keyframes rotation {
		0% {
			transform: rotate(-360deg);
		}
		100% {
			transform: rotate(360deg);
		}
	}
	@keyframes rotation-ccw {
		0% {
			transform: rotate(360deg);
		}
		100% {
			transform: rotate(-360deg);
		}
	}
	.circle-transform-cw {
		animation: rotation 8s linear infinite;
	}
	.circle-transform-ccw {
		animation: rotation-ccw 8s linear infinite;
	}
	ul.chapters {
		margin: 0;
		padding: 0;
		list-style-type: none;
	}
	ul.slideshows {
		margin: 0;
		padding-bottom: 1rem;
		padding-left: 1rem;
		list-style-type: none;
	}
	@media all and (max-width: 700px) {
		ul.chapters {
			margin-top: 1rem;
		}
		.menu-items {
			order: 1;
			height: auto;
			width: 100%;
		}
		.logo {
			order: 2;
			height: auto;
			width: 100%;
		}
	}
</style>
