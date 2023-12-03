<script lang="ts">
	import { Switch, Icon, SelectField, Input, Field, Button, Toggle } from 'svelte-ux';
	import checkIcon from '$lib/check.svg';
	import { createEventDispatcher } from 'svelte';

	let liveMode = true;
	let fetchVideoDate;
	const dispatch = createEventDispatcher();

	let minutesToLoad: number;
	$: {
		if (liveMode !== true) {
			dispatch('liveMode', false);
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

			minutesToLoad = 10;
		} else {
			dispatch('liveMode', true);

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
</script>

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

<style>
	.video-selection-form {
		display: flex;
		flex: 1;
		flex-direction: column;
		gap: 12px;
		padding: 24px;
	}
</style>
