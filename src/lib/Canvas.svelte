<script lang="ts">
	import { createEventDispatcher, onMount } from 'svelte';
	import { fabric } from 'fabric';
	import sampleImage from '$lib/sc.png';

	export let section: any;
	let width = section.offsetWidth;
	let height = section.offsetHeight;

	let faceImage, bodyImage;

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
		// recalculatedData =
		let canvas = new fabric.Canvas('c');
		let faceCanvas = new fabric.Canvas('face');
		let bodyCanvas = new fabric.Canvas('body');
		// 	person_id: create_UUID(),
		// 	left: (data.x * section.offsetWidth) / 1920,
		// 	top: (data.y * section.offsetHeight) / 1080,
		// 	width: data.width,
		// 	height: data.height
		// }));

		recalculatedData = [
			{
				person_id: create_UUID(),
				left: (1046 * section.offsetWidth) / 1512,
				top: (504 * section.offsetHeight) / 853,
				width: 30,
				height: 140,
				face_url: '/face-3.png',
				body_url: '/body-3.png'
			},
			{
				person_id: create_UUID(),
				left: (1094 * section.offsetWidth) / 1512,
				top: (504 * section.offsetHeight) / 853,
				width: 30,
				height: 140,
				face_url: '',
				body_url: ''
			}
		];

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
				if (rect.get('face_url') !== '' && rect.get('body_url') !== '') {
					dispatch('person', {
						id: rect.get('person_id'),
						date: '18/11/2023, 5:01:46 PM',
						camera: 'Outdoor camera 1',
						ppi: 24,
						face: rect.get('face_url'),
						body: rect.get('body_url'),
						detectedTimeFrom: '18/11/2023, 5:01:46 PM',
						detectedTimeTo: '18/11/2023, 5:03:16 PM'
					});
				} else {
					//get face and body from canvas

					new fabric.Image.fromURL(sampleImage, function (oImg) {
						oImg.scaleToWidth(1512);
						oImg.set({
							left: -1094,
							top: -504,
							selectable: false
						});

						faceCanvas.add(oImg);
						faceImage = faceCanvas.toDataURL({ format: 'jpeg', width: 35, height: 38 });
					});

					new fabric.Image.fromURL(sampleImage, function (oImg) {
						oImg.scaleToWidth(1512);
						oImg.set({
							left: -1094,
							top: -538,
							selectable: false
						});

						bodyCanvas.add(oImg);
						bodyImage = bodyCanvas.toDataURL({ format: 'jpeg', width: 35, height: 170 });
					});

					setTimeout(() => {
						dispatch('person', {
							id: rect.get('person_id'),
							date: '18/11/2023, 5:01:46 PM',
							camera: 'Outdoor camera 1',
							ppi: 24,
							face: faceImage,
							body: bodyImage,
							detectedTimeFrom: '18/11/2023, 5:01:46 PM',
							detectedTimeTo: '18/11/2023, 5:03:16 PM'
						});
					}, 1000);
				}
			});
			canvas.add(rect);
		});
	});
</script>

<canvas id="c" {width} {height} class="detected_boxes" />
<img src={sampleImage} alt="detected screen" class="detected_image" />
<canvas id="face" style="display:none" />
<canvas id="body" style="display:none" />

<style>
	.detected_boxes {
		position: absolute;
		width: 100%;
		top: 0;
		left: 0;
		z-index: 20;
	}
	.detected_image {
		position: absolute;
		width: 100%;
		top: 0;
		left: 0;
		z-index: 10;
	}
</style>
