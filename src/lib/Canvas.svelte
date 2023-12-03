<script lang="ts">
	import { createEventDispatcher, onMount } from 'svelte';
	import { fabric } from 'fabric';

	export let section: any;
	let width = section.offsetWidth;
	let height = section.offsetHeight;

	const dispatch = createEventDispatcher();
	let recievedData: object[] = [];

	function create_UUID() {
		let dt = new Date().getTime();
		let uuid = 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function (c) {
			let r = (dt + Math.random() * 16) % 16 | 0;
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
			person_id: create_UUID(),
			left: (data.x * section.offsetWidth) / 1920,
			top: (data.y * section.offsetHeight) / 1080,
			width: data.width,
			height: data.height
		}));

		let canvas = new fabric.Canvas('c');
		recalculatedData.forEach((data) => {
			let rect = new fabric.Rect({
				...data,
				fill: 'white',
				opacity: 0.7,
				selectable: false,
				stroke: 'white'
			});
			rect.on('mouseover', function () {
				rect.bringToFront();
				rect.set({ fill: 'blue' });
			});
			rect.on('mouseout', function () {
				rect.bringToFront();
				rect.set({ fill: 'white' });
			});
			rect.on('mousedown', function () {
				// dispatch('person', rect.get('person_id'));
				dispatch('person', {
					date: '18/11/2023, 5:01:46 PM',
					camera: 'Auditorium Entrance camera 1',
					ppi: 24,
					face: '/face-1.png',
					body: '/body-1.png'
				});
			});
			canvas.add(rect);
		});
	});
</script>

<canvas id="c" style="border:1px solid #ccc" {width} {height} />
