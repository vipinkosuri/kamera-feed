<script lang="ts">
	import { Button, Toggle } from 'svelte-ux';
	import PlaceholderImage from '$lib/placeholder.png';
	import download from '$lib/download.png';
	import camera from '$lib/camera.svg';
	import calendarClock from '$lib/calendar-clock.svg';
	import view from '$lib/view.svg';

	export let personDetected = false;
	export let liveMode;
	export let person: {} = {};

	const saveImage = (person: object) => {
		let savedImages = JSON.parse(localStorage.getItem('savedImages') || '[]');

		if (savedImages.filter((e) => e.id === person.id).length == 0) {
			savedImages.push(person);
			localStorage.setItem('savedImages', JSON.stringify(savedImages));
		}
	};
</script>

<div class="container" class:disabled-content={liveMode}>
	<div class="container-heading">
		<h3>person detected</h3>
		{#if personDetected}
			<ul class="content">
				<li class="content-info">
					<img src={calendarClock} alt="download icon" /><span class="info">{person.date}</span>
				</li>
				<li class="content-info">
					<img src={camera} alt="download icon" /><span class="info">{person.camera}</span>
				</li>
				<li class="content-info">
					<img src={view} alt="download icon" /><span class="info">{person.ppi} PPI</span>
				</li>
			</ul>
		{/if}
	</div>
	<div class="container-content">
		{#if !personDetected}
			<div class="no-content">
				<img src={PlaceholderImage} alt="icon to show images" />
				<p>People detected in the camera<br /> will be shown here</p>
			</div>
		{:else if person}
			<div class="person-images">
				<ul class="person-image">
					<li>
						<div class="section-heading">
							<p>Face</p>
							<a href={person.face} download><img src={download} alt="download icon" /></a>
						</div>
						<img src={person.face} alt="face" />
					</li>
					<li>
						<div class="section-heading">
							<p>Body</p>
							<a href={person.body} download><img src={download} alt="download icon" /></a>
						</div>
						<img src={person.body} alt="body" />
					</li>
				</ul>
			</div>
			<div class="center-content">
				<Button variant="outline" color="accent" rounded="full" on:click={() => saveImage(person)}
					>+ Save Image</Button
				>
			</div>
		{/if}
	</div>
</div>

<style>
</style>
