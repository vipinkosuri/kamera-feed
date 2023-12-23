<script lang="ts">
	import { Button, Toggle } from 'svelte-ux';
	import PlaceholderImage from '$lib/placeholder.png';
	import download from '$lib/download.png';
	import camera from '$lib/camera.svg';
	import calendarClock from '$lib/calendar-clock.svg';
	import view from '$lib/view.svg';
	import { onMount } from 'svelte';

	export let liveMode;
	let savedImages: object[] = [];

	$: {
		// if (liveMode === true) {
		// 	savedImages = [];
		// } else {
	}
	// }

	onMount(() => {
		savedImages = JSON.parse(localStorage.getItem('savedImages') || '[]');
	});
</script>

<div class="container" class:disabled-content={liveMode}>
	<div class="container-heading">
		<h3>Saved Images</h3>
	</div>
	{#if savedImages.length == 0}
		<div class="container-content">
			<div class="no-content">
				<img src={PlaceholderImage} alt="icon to show images" />
				<p>People detected in the camera<br /> will be shown here</p>
			</div>
		</div>
	{:else}
		<div class="images">
			{#each savedImages as person}
				<div class="person">
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
				</div>
				<div class="person">
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
				</div>
				<div class="person">
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
				</div>
				<div class="person">
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
				</div>
			{/each}
		</div>
	{/if}
</div>

<style>
	.images {
		display: flex;
		padding: 20px;
		overflow-scroll-behavior: smooth;
		overflow-x: scroll;
		overflow-y: hidden;
	}
	.person {
		width: 322px;
		border-right: solid 1px #ccc;
	}
</style>
