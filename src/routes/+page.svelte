<script lang="ts">
	import Hls from 'hls.js/dist/hls.min';
	import { onMount } from 'svelte';

	let video: HTMLMediaElement;
	let MyCanvas: any;
	let showCanvas = false;
	let section;

	let uuid: string = 'Pause & Click a box to show UUID';

	onMount(async () => {
		MyCanvas = (await import('$lib/Canvas.svelte')).default;
		// if (Hls.isSupported()) {
		// 	const hls = new Hls({
		// 		debug: true,
		// 		maxBufferSize: 120,
		// 		maxBufferLength: 60,
		// 		maxMaxBufferLength: 60
		// 	});
		// 	hls.loadSource('/src/lib/hls-video/prog_index.m3u8');
		// 	if (video) {
		// 		hls.attachMedia(video);
		// 		hls.on(Hls.Events.MEDIA_ATTACHED, () => {
		// 			video.muted = true;
		// 			video.play();
		// 		});
		// 		// render()
		// 	}
		// }
		if (Hls.isSupported()) {
			var hls = new Hls();
			hls.on(Hls.Events.MEDIA_ATTACHED, function () {
				console.log('video and hls.js are now bound together !');
			});
			hls.on(Hls.Events.MANIFEST_PARSED, function (event, data) {
				console.log('manifest loaded, found ' + data.levels.length + ' quality level');
			});
			hls.loadSource('/hls-video/prog_index.m3u8');
			// bind them together
			hls.attachMedia(video);
		}
	});
</script>

<svelte:head>
	<title>Home</title>
</svelte:head>

<section bind:this={section}>
	<video
		muted
		autoplay
		controls
		bind:this={video}
		on:pause={() => (showCanvas = true)}
		on:play={() => (showCanvas = false)}
	/>
	{#if showCanvas}
		<div class="video_position">
			<svelte:component this={MyCanvas} {section} on:uuid={(event) => (uuid = event.detail)} />
		</div>
	{/if}
</section>
<h2>{uuid}</h2>

<style>
	h2 {
		text-align: center;
	}
	section {
		position: relative;
	}
	video {
		width: 100%;
	}
	.video_position {
		position: absolute;
		top: 0;
		right: 0;
		bottom: 40px;
		left: 0;
		width: 100%;
		height: 86%;
		overflow: hidden;
	}
</style>
