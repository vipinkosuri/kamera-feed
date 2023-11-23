<script lang="ts">
	// import Hls from 'hls.js/dist/hls.min';
	import { onMount } from 'svelte';
	// import hlsPlaylist from '$lib/hls-video/prog_index.m3u8';

	let video: HTMLMediaElement;

	onMount(() => {
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
		var videoSrc = '/src/lib/hls-video/playlist.m3u8';
		if (Hls.isSupported()) {
    var hls = new Hls();
    hls.on(Hls.Events.MEDIA_ATTACHED, function () {
      console.log('video and hls.js are now bound together !');
    });
    hls.on(Hls.Events.MANIFEST_PARSED, function (event, data) {
      console.log(
        'manifest loaded, found ' + data.levels.length + ' quality level',
      );
    });
    hls.loadSource('/hls-video/prog_index.m3u8');
    // bind them together
    hls.attachMedia(video);
		// video.play()
  }

	});
</script>

<svelte:head>
	<title>Home</title>
</svelte:head>

<section>
	<video bind:this={video} controls width="720"/>
</section>

<style>
	section {
		height: 100vh;
		display: flex;
		align-items: center;
		justify-content: center;
	}
</style>
