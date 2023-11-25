<script lang="ts">
	import { createEventDispatcher, onMount } from 'svelte';
	import { Stage, Group, Layer, Rect } from 'svelte-konva';
	import { tick } from 'svelte';

	export let section: any;
	// let stage;

	// const dispatch = createEventDispatcher();
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

	let recalculatedData;

	// function handleHover(e: Event) {
	// 	const konvaElement = (<CustomEvent>e).detail.target;
	// 	const stage = konvaElement.getStage();
	// 	stage.container().style.cursor = 'pointer';
	// 	konvaElement.fill('blue');
	// 	konvaElement.moveToTop();
	// }

	// function handleMouseOut(e: Event) {
	// 	const konvaElement = (<CustomEvent>e).detail.target;
	// 	const stage = konvaElement.getStage();
	// 	stage.container().style.cursor = 'default';
	// 	konvaElement.fill('white');
	// }

	// function fetchApi(e: Event) {
	// 	const konvaElement = (<CustomEvent>e).detail.target;
	// 	dispatch('uuid', konvaElement.id());
	// }

	onMount(async () => {
		recalculatedData = recievedData.map((data) => ({
			id: create_UUID(),
			x: (data.x * section.offsetWidth) / 1920,
			y: (data.y * section.offsetHeight) / 1080,
			width: data.width,
			height: data.height
		}));
		var width = section.offsetWidth;
		var height = section.offsetHeight;

		var stage = new Konva.Stage({
			container: 'container',
			width: width,
			height: height
		});

		var layer = new Konva.Layer();
		recalculatedData.forEach((data) => {
			var rect1 = new Konva.Rect({
				...data,
				fill: 'green',
				stroke: 'black',
				strokeWidth: 4
			});
			rect1.on('mouseover', function () {
				stage.container().style.cursor = 'pointer';
				rect1.fill('blue');
				rect1.moveToTop();
			});
			layer.add(rect1);
		});

		// add the shape to the layer

		// add the layer to the stage
		stage.add(layer);
		// await tick();
	});
</script>

<div id="container" />

<!-- <Stage config={{ width: section.offsetWidth, height: section.offsetHeight }}>
	<Layer>
		<Group>
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
		</Group>
	</Layer>
</Stage> -->
