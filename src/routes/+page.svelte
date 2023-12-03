<script lang="ts">
	import PersonDetected from './PersonDetected.svelte';

	import StreamSelector from './StreamSelector.svelte';
	import Hls from 'hls.js';
	import { onMount } from 'svelte';

	let video: HTMLMediaElement;
	let MyCanvas: any;
	let showCanvas = false;

	let section;
	let personDetected = false;
	let person: {} = {};
	$: liveMode = true;
	$: {
		if (liveMode == true) {
			personDetected = false;
			person = {};
		}
	}

	onMount(async () => {
		MyCanvas = (await import('$lib/Canvas.svelte')).default;

		// 		debug: true,
		// 		maxBufferSize: 120,
		// 		maxBufferLength: 60,
		// 		maxMaxBufferLength: 60
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
			// hls.on(Hls.Events.BUFFER_APPENDING, (event, data) => {
			// 	console.log(data);
			// 	console.log('segment added to buffer');
			// });

			hls.on(Hls.Events.FRAG_CHANGED, (event, data) => {
				console.log(data);
				console.log('segment added to buffer');
			});
			hls.on(Hls.Events.ERROR, (event, data) => {
				var errorType = data.type;
				var errorDetails = data.details;
				var errorFatal = data.fatal;
				console.log(errorType, errorDetails, errorFatal);

				switch (data.details) {
					case Hls.ErrorDetails.FRAG_LOAD_ERROR:
						console.log('Failed segment:', data.frag);
						// video.currentTime = data.frag.start + data.frag.duration + 0.1;
						break;
				}
			});
		}
	});
</script>

<svelte:head>
	<title>Home</title>
</svelte:head>
<main>
	<section bind:this={section}>
		<video
			muted
			autoplay
			playsinline
			controls
			bind:this={video}
			on:pause={() => (!liveMode ? (showCanvas = true) : '')}
			on:play={() => (!liveMode ? (showCanvas = false) : '')}
		/>
		{#if showCanvas}
			<div class="video_position">
				<svelte:component
					this={MyCanvas}
					{section}
					on:person={(event) => {
						person = event.detail;
						personDetected = true;
					}}
				/>
			</div>
		{/if}
	</section>
	<div class="container"><h3 class="container-heading">saved images</h3></div>
</main>
<aside>
	<StreamSelector on:liveMode={(event) => (liveMode = event.detail)} />
	<PersonDetected {personDetected} {person} {liveMode} />
	{liveMode}
</aside>

<style>
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
