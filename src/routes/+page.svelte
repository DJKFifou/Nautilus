<script lang="ts">
	let video: HTMLVideoElement | null = null;
	let menuOpen: boolean = false;
	let sound: HTMLImageElement | null = null;

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
		if (!video) return
		if (menuOpen) {
			menuOpen = false;
		} else {
			if (video.paused) {
				video.play();
			} else { video.pause();
			}
		}
	}

	function openMenu(): void {
		if (window.innerWidth < 768) menuOpen = true;
	}

	function toggleSound() {
		if (!video) return;
		if (video.muted === true) {
			video.muted = false;
			if (sound) sound.src = "/src/lib/assets/svg/sound.svg"
		} else {
			video.muted = true;
			if (sound) sound.src = "/src/lib/assets/svg/sound-muted.svg"
		}
	}

</script>

<div class="relative h-screen w-full">
	<video bind:this={video} on:click={toggleVideo} src="video.mp4" autoplay loop muted class="video h-full w-full object-cover bg-black"></video>

	<h1 class="absolute left-1/2 top-6.5 -translate-x-1/2 p-4 md:px-8 z-50 md:text-2xl text-white uppercase border-[0.5px] border-white rounded-lg md:rounded-2xl bg-white/1 backdrop-blur-[6px] font-title text-trim font-extrabold">
		Nautilus
	</h1>
	
	<button on:click={toggleSound} class="absolute top-6.5 right-6 px-4 py-4.5 rounded-lg border-[0.5px] border-white bg-white/1 backdrop-blur-[6px] cursor-pointer">
		<img bind:this={sound} src="/src/lib/assets/svg/sound-muted.svg" alt="" class="h-4 w-4">
	</button>

	<div on:click={openMenu} class="absolute left-3.5 md:left-1/2 bottom-3.5 md:bottom-7 md:-translate-x-1/2 z-50 md:w-full md:px-13 text-xl text-white uppercase font-title cursor-pointer transition-all duration-500 ease-out {menuOpen ? 'max-w-fit' : 'max-w-8'} md:max-w-full">
		<div class="relative flex flex-col md:flex-row items-center justify-between gap-8 w-full bg-white/1 backdrop-blur-[6px] px-4 py-6 md:px-8 md:py-4 border-[0.5px] border-white rounded-lg md:rounded-2xl overflow-hidden">
			<button class="absolute left-1/2 top-1/2 -translate-1/2 all duration-300 ease-out {menuOpen ? 'translate-x-20' : '-translate-x-1/2'}">></button>
			{#each paliers as palier (palier.id)}
				<button
					class="cursor-pointer transform transition-all duration-500 ease-out
					{menuOpen ? 'opacity-100 translate-x-0 animate-slide-in' : 'opacity-0 -translate-x-8 pointer-events-none'}"
					on:click={() => changeCurrentTime(palier.time)}>
					{palier.name}
				</button>
			{/each}
		</div>
	</div>
</div>