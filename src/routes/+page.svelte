<script lang="ts">
	import { browser } from '$app/environment';
	import { onMount, onDestroy } from 'svelte';

	let video: HTMLVideoElement | null = null;
	let menuOpen = false;
	let sound: HTMLImageElement | null = null;
	let isWide = false;
	let isLoading: boolean = true;

	if (browser) isWide = window.innerWidth > 768;

	const paliers = [
		{ id: 1, name: "Calme", time: 0 },
		{ id: 2, name: "Agitation", time: 3.5 },
		{ id: 3, name: "Solitude", time: 7 },
		{ id: 4, name: "Étouffement", time: 10.5 },
		{ id: 5, name: "Infinité", time: 14 }
	];

	function changeCurrentTime(time: number): void {
		if (video) video.currentTime = time;
	}

	function toggleVideo(): void {
		if (!video) return;
		if (menuOpen) {
			menuOpen = false;
		} else {
			if (video.paused) {
				video.play();
			} else {
				video.pause();
			}
		}
	}

	function openMenu(): void {
		if (window.innerWidth < 768) menuOpen = true;
	}

	function toggleSound(): void {
		if (!video) return;
		video.muted = !video.muted;
		if (sound) {
			sound.src = video.muted
				? "/assets/svg/sound-muted.svg"
				: "/assets/svg/sound.svg";
		}
	}

	function handleResize(): void {
		isWide = window.innerWidth > 768;
		if (isWide && menuOpen) menuOpen = false;
	}

	function handleLoaded() {
		isLoading = false;
	}

	function waitForVideoReady(video: HTMLVideoElement): Promise<void> {
		return new Promise((resolve) => {
			if (video.readyState >= 3) {
				resolve();
			} else {
				const handler = (event: Event) => {
					video.removeEventListener('canplay', handler);
					video.removeEventListener('loadeddata', handler);
					resolve();
				};
				video.addEventListener('canplay', handler);
				video.addEventListener('loadeddata', handler);
			}
		});
	}

	onMount(() => {
		if (!browser) return;

		window.addEventListener('resize', handleResize);

		const timeout = setTimeout(() => {
			if (isLoading) isLoading = false;
		}, 2000);

		(async () => {
			if (video) {
				await waitForVideoReady(video);
				isLoading = false;
			}
		})();

		return () => {
			clearTimeout(timeout);
			window.removeEventListener('resize', handleResize);
		};
	});

	onDestroy(() => {
		if (browser) window.removeEventListener('resize', handleResize);
	});
</script>


<div class="relative h-dvh w-full">
	<video bind:this={video} onclick={toggleVideo} oncanplay={handleLoaded} onloadeddata={handleLoaded} src="/assets/motion.mp4" autoplay loop muted playsinline class="video h-full w-full object-cover bg-black"></video>

	<h1 class="absolute left-1/2 top-6.5 -translate-x-1/2 p-4 md:px-8 z-50 md:text-2xl text-white uppercase border-[0.5px] border-white rounded-lg md:rounded-2xl bg-white/1 backdrop-blur-[6px] font-title text-trim font-extrabold">
		Nautilus
	</h1>
	
	<button onclick={toggleSound} class="absolute top-6.5 right-6 px-4 py-4.5 rounded-lg border-[0.5px] border-white bg-white/1 backdrop-blur-[6px] cursor-pointer">
		<img bind:this={sound} src="/assets/svg/sound-muted.svg" alt="" class="h-4 w-4">
	</button>

	<div onclick={openMenu} class="absolute left-3.5 md:left-1/2 bottom-3.5 md:bottom-7 md:-translate-x-1/2 z-50 md:w-full md:px-13 text-xl text-white uppercase font-title cursor-pointer md:cursor-auto transition-all duration-500 ease-out {menuOpen ? 'max-w-fit' : 'max-w-8'} md:max-w-full">
		<div class="relative flex flex-col md:flex-row items-center justify-between gap-8 w-full bg-white/1 backdrop-blur-[6px] px-4 py-6 md:px-8 md:py-4 border-[0.5px] border-white rounded-lg md:rounded-2xl overflow-hidden">
			<img src="/assets/svg/caret.svg" class="md:hidden absolute left-1/2 top-1/2 -translate-1/2 all duration-300 ease-out {menuOpen ? 'translate-x-20' : '-translate-x-1/2'}">
			{#each paliers as palier (palier.id)}
				<button
					class="cursor-pointer transform transition-all duration-500 ease-out
					{menuOpen || isWide ? 'opacity-100 translate-x-0' : 'opacity-0 -translate-x-8 pointer-events-none'}"
					onclick={() => changeCurrentTime(palier.time)}>
					{palier.name}
				</button>
			{/each}
		</div>
	</div>
</div>

{#if isLoading}
	<div class="absolute inset-0 flex items-center justify-center bg-black z-50">
		<div class="w-12 h-12 border-4 border-white border-t-transparent rounded-full animate-spin"></div>
	</div>
{/if}