<script lang="ts">
	import { createEventDispatcher } from 'svelte';
	import { Stage, Layer, Rect } from 'svelte-konva';

	export let section: any;

	const dispatch = createEventDispatcher();
	let recievedData: object[] = [];

	function create_UUID() {
		var dt = new Date().getTime();
		var uuid = 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function (c) {
			var r = (dt + Math.random() * 16) % 16 | 0;
			dt = Math.floor(dt / 16);
			return (c == 'x' ? r : (r & 0x3) | 0x8).toString(16);
		});
		return uuid;
	}

	for (let index = 0; index < 25; index++) {
		recievedData.push({
			id: `${Math.floor(Math.random() * 1000)}`,
			x: Math.random() * 1920,
			y: Math.random() * 1080,
			width: 25,
			height: 110
		});
	}

	let recalculatedData = recievedData.map((data) => ({
		id: create_UUID(),
		x: (data.x * section.offsetWidth) / 1920,
		y: (data.y * section.offsetWidth) / 1080,
		width: data.width,
		height: data.height
	}));

	function handleHover(e: Event) {
		const konvaElement = (<CustomEvent>e).detail.target;
		const stage = konvaElement.getStage();
		stage.container().style.cursor = 'pointer';
		konvaElement.fill('blue');
		konvaElement.moveToTop();
	}

	function handleMouseOut(e: Event) {
		const konvaElement = (<CustomEvent>e).detail.target;
		const stage = konvaElement.getStage();
		stage.container().style.cursor = 'default';
		konvaElement.fill('white');
	}

	function fetchApi(e: Event) {
		const konvaElement = (<CustomEvent>e).detail.target;
		dispatch('uuid', konvaElement.id());
	}
</script>

<Stage config={{ width: section.offsetWidth, height: section.offsetHeight }}>
	<Layer>
		{#each recalculatedData as data}
			<Rect
				config={{
					x: data.x,
					y: data.y,
					width: data.width,
					height: data.height,
					opacity: 0.6,
					fill: 'rgba(255,255,255,0.7)',
					stroke: 'white',
					id: data.id
				}}
				on:mouseover={handleHover}
				on:mouseout={handleMouseOut}
				on:pointerclick={fetchApi}
			/>
		{/each}
	</Layer>
</Stage>
