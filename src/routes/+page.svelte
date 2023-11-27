<script lang="ts">
	import Hls from 'hls.js';
	import { onMount } from 'svelte';
	import { Switch, Icon, SelectField, Input, Field, Button, Toggle } from 'svelte-ux';
	import checkIcon from '$lib/check.svg';

	let video: HTMLMediaElement;
	let MyCanvas: any;
	let showCanvas = false;
	let section;
	let liveMode = true;
	let fetchVideoDate;
	let minutesToLoad: number;
	$: {
		if (liveMode !== true) {
			let currentDate = new Date(new Date() - 10 * 60000);
			//yyyy-MM-ddThh:mm
			fetchVideoDate =
				currentDate.getFullYear() +
				'-' +
				(currentDate.getMonth() + 1) +
				'-' +
				currentDate.getDate() +
				'T' +
				(currentDate.getHours().toString().length === 1
					? '0' + currentDate.getHours()
					: currentDate.getHours()) +
				':' +
				(currentDate.getMinutes().toString().length === 1
					? '0' + currentDate.getMinutes()
					: currentDate.getMinutes());

			console.log(fetchVideoDate);
			minutesToLoad = 10;
		} else {
			fetchVideoDate = null;
			minutesToLoad = 0;
		}
	}

	let uuid: string = 'Pause & Click a box to show UUID';
	let options: object[] = [
		{ name: 'cameraOne', value: 'Camera 1' },
		{ name: 'cameraTwo', value: 'Camera 2' },
		{ name: 'cameraThree', value: 'Camera 3' }
	];
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
<main>
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
	<div class="container"><h3 class="section-heading">saved images</h3></div>
</main>
<aside>
	<div class="container">
		<form class="video-selection-form" action="POST">
			<div class="grid gap-2 place-content-center">
				<label class="flex gap-2 items-center text-sm" for="live-switch">
					<Switch bind:checked={liveMode} let:checked id="live-switch">
						{#if checked}
							<Icon path={checkIcon} size=".8em" />
						{/if}
					</Switch>
					<p>Live</p>
				</label>
			</div>

			<Field label="Select Camera" let:id>
				<select {id} class="text-sm w-full outline-none appearance-none cursor-pointer">
					<option value={1}>First</option>
					<option value={2}>Second</option>
					<option value={3}>Third</option>
					<option value={4}>Fourth</option>
				</select>
				<span slot="append">
					<Icon path={checkIcon} />
				</span>
			</Field>
			<Field label="Select date and start time" let:id disabled={liveMode}>
				<Input bind:value={fetchVideoDate} type="datetime-local" />
			</Field>
			<Field label="Enter number of minutes to load" let:id disabled={liveMode}>
				<Input bind:value={minutesToLoad} type="text" pattern="\d*" maxlength="3" />
			</Field>
			<Toggle let:on={loading} let:toggle>
				<Button
					icon={checkIcon}
					disabled={liveMode}
					variant="fill"
					color="accent"
					{loading}
					on:click={toggle}
					rounded="full">Load</Button
				>
			</Toggle>
		</form>
	</div>
	<div class="container">
		<h3 class="section-heading">person detected</h3>
		<ul>
			<li><span>Icon</span><span class="info">Person info</span></li>
			<li><span>Icon</span><span class="info">Person info</span></li>
			<li><span>Icon</span><span class="info">Person info</span></li>
		</ul>
	</div>
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

	.container {
		background-color: white;
		border-radius: 12px;
		box-shadow: 0px 1px 2px rgba(0, 0, 0, 0.05);
	}

	.video-selection-form {
		display: flex;
		flex: 1;
		flex-direction: column;
		gap: 12px;
		padding: 24px;
	}
</style>
