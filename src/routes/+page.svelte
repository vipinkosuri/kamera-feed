<script lang="ts">
	import { onMount } from 'svelte';

	import SavedImages from './SavedImages.svelte';
	import PersonDetected from './PersonDetected.svelte';
	import StreamSelector from './StreamSelector.svelte';

	import Hls from 'hls.js';

	let video: HTMLMediaElement;
	let toggleButton: HTMLButtonElement;
	let progress: HTMLDivElement;
	let progressBar: HTMLDivElement;
	let detectedZone: HTMLDivElement;
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

	function togglePlay() {
		if (video.paused || video.ended) {
			video.play();
		} else {
			video.pause();
		}
	}
	function updateToggleButton() {
		toggleButton.innerHTML = video.paused ? '►' : '❚❚';
	}

	function handleProgress() {
		const progressPercentage = (video.currentTime / video.duration) * 100;
		progressBar.style.flexBasis = `${progressPercentage}%`;
	}

	function scrub(e) {
		const scrubTime = (e.offsetX / progress.offsetWidth) * video.duration;
		video.currentTime = scrubTime;
		// trigger function to get new screenshot and boxes
	}

	onMount(async () => {
		MyCanvas = (await import('$lib/Canvas.svelte')).default;

		// 		debug: true,
		// 		maxBufferSize: 120,
		// 		maxBufferLength: 60,
		// 		maxMaxBufferLength: 60
		// let canvas = new fabric.Canvas('face');

		// new fabric.Image.fromURL(sampleImage, function (oImg) {
		// 	oImg.scaleToWidth(1512);
		// 	canvas.setWidth(35);
		// 	canvas.setHeight(38);

		// 	oImg.set({
		// 		left: -1061,
		// 		top: -502,
		// 		selectable: false
		// 	});

		// 	canvas.add(oImg);
		// 	generatedImage = canvas.toDataURL();
		// });

		toggleButton.addEventListener('click', togglePlay);
		video.addEventListener('click', togglePlay);
		video.addEventListener('play', updateToggleButton);
		video.addEventListener('pause', updateToggleButton);
		video.addEventListener('timeupdate', handleProgress);
		progress.addEventListener('click', scrub);
		let mousedown = false;
		progress.addEventListener('mousedown', () => (mousedown = true));
		progress.addEventListener('mousemove', (e) => mousedown && scrub(e));
		progress.addEventListener('mouseup', () => (mousedown = false));

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
				// console.log(data);
			});
			hls.on(Hls.Events.ERROR, (event, data) => {
				var errorType = data.type;
				var errorDetails = data.details;
				var errorFatal = data.fatal;
				// console.log(errorType, errorDetails, errorFatal);

				switch (data.details) {
					case Hls.ErrorDetails.FRAG_LOAD_ERROR:
						// console.log('Failed segment:', data.frag);
						// video.currentTime = data.frag.start + data.frag.duration + 0.1;
						break;
				}
			});
		}
	});
</script>

<svelte:head>
	<title>Fovea::Home</title>
</svelte:head>
<main>
	<!-- show canvas image in detected person only if person url is absent in the object -->

	<!-- <canvas id="face" style="display:none" />
	<canvas id="body" style="display:none" />
	<img src={generatedImage} alt="face image" style="width: 100px" /> -->

	<section bind:this={section}>
		<div class="video-player">
			<video
				muted
				autoplay
				playsinline
				class="video"
				bind:this={video}
				on:pause={() => (!liveMode ? (showCanvas = true) : '')}
				on:play={() => (!liveMode ? (showCanvas = false) : '')}
			/>
		</div>

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
	<div class="controls">
		<div class="progress" bind:this={progress}>
			<div class="progress__filled" bind:this={progressBar} />
			<div class="detected_zone" bind:this={detectedZone} />
		</div>
		<button class="controls__button toggleButton" title="Toggle Play" bind:this={toggleButton}
			>►</button
		>
	</div>
	<SavedImages {liveMode} />
</main>
<aside>
	<StreamSelector on:liveMode={(event) => (liveMode = event.detail)} />
	<PersonDetected {personDetected} {person} {liveMode} />
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
		height: 100%;
		overflow: hidden;
	}

	.progress {
		flex: 10;
		position: relative;
		display: flex;
		flex-basis: 100%;
		height: 5px;
		/* background: rgba(0,0,0,0.5); */
		background: rgba(229, 231, 235, 1);
		cursor: pointer;
		height: 15px;
		height: 12px;
	}

	.progress__filled {
		background: rgba(31, 41, 55, 1);
		flex: 0;
		flex-basis: 0%;
	}
</style>
