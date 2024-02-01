<script setup lang="ts">
import Header from './components/Header.vue'
import Switch from "./components/Switch.vue";
import {onMounted, ref} from "vue";

const sidebarOpen = ref(false);

function randomInRange(min: number, max: number) {
	return Math.random() * (max - min) + min;
}

function RED() {
	return `rgba(${randomInRange(200, 255)}, 200, 200, ${randomInRange(0.01, 0.3)})`;
}
function WHITE() {
	return `rgba(255, 255, 255, ${Math.random()})`;
}
function BLUE() {
	return `rgba(200, 200, ${randomInRange(200, 255)}, ${randomInRange(0.01, 0.3)})`;
}

interface Vector2 {
	x: number;
	y: number;
}

class Planet {
	position: Vector2 = {
		x: 0,
		y: 0
	};
	radius: number = 0;
	color: string = 'white';
	blur: number = 0;
	hasShadow: boolean = false;

	constructor(radius: number, color: string, blur: number, hasShadow?: boolean) {
		this.radius = radius;
		this.color = color;
		this.blur = blur;
		if(hasShadow !== undefined)
			this.hasShadow = hasShadow;
	}

	draw(ctx: CanvasRenderingContext2D) {

		ctx.fillStyle = this.color;
		ctx.filter = `blur(${this.blur}px)`;
		ctx.beginPath();
		// draw a half circle, then draw another half circle to simulate a shadow
		ctx.arc(this.position.x, this.position.y, this.radius, 0, Math.PI * 2);
		ctx.fill();
		ctx.closePath();

		if(this.hasShadow) {
			// draw shadow
			ctx.fillStyle = 'rgba(0, 0, 0, 0.2)';
			ctx.filter = 'none';
			ctx.beginPath();
			// draw a crescent moon shape
			ctx.arc(this.position.x, this.position.y, this.radius, -1.3, 1.8);
			ctx.fill();
			ctx.closePath();
		}
	}
}

onMounted(() => {
	const canvas = document.querySelector('.game') as HTMLCanvasElement;
	const ctx = canvas.getContext('2d') as CanvasRenderingContext2D;

	canvas.addEventListener("click", () => {
		// sidebarOpen.value = !sidebarOpen.value;
		generatePlanets(canvas, ctx);
	});

	generatePlanets(canvas, ctx);
});

function generatePlanets(canvas: HTMLCanvasElement, ctx: CanvasRenderingContext2D) {
	const planets: Planet[] = [];

	canvas.width = window.innerWidth;
	canvas.height = window.innerHeight - 48;

	ctx.clearRect(0, 0, canvas.width, canvas.height);

	ctx.fillStyle = 'rgba(18, 18, 18, 0.8)';
	ctx.fillRect(0, 0, canvas.width, canvas.height);

	// generate a starry background
	for(let i = 0; i < 500; i++) {
		const x = Math.random() * canvas.width;
		const y = Math.random() * canvas.height;
		const radius = Math.random() * 2;
		ctx.beginPath();
		ctx.arc(x, y, radius, 0, Math.PI * 2);
		// pick red, white, or blue
		// then shade it with a random alpha
		let color = Math.random() > 0.5 ? RED() : Math.random() > 0.5 ? WHITE() : BLUE();
		ctx.fillStyle = color;
		ctx.fill();
		ctx.closePath();
	}

	const offset = 100;
	let totalWidth = 0;

	// generate some planets
	const sunRadius = randomInRange(20, 200);

	// sun color goes from small radius = white to large radius = red with yellow and blue in between
	const sunColor = sunRadius < 60 ? "white" : sunRadius < 100 ? "blue" : sunRadius < 140 ? "yellow" : sunRadius < 180 ? "orange": 'red';

	// blur is opposite of radius, so the larger the sun, the less blur
	const sunBlur = sunRadius < 60 ? 10 : sunRadius < 100 ? 5 : sunRadius < 140 ? 2 : sunRadius < 180 ? 1 : 0;

	planets.push(new Planet(sunRadius, sunColor, sunBlur));
	for(let i = 0; i < randomInRange(0, canvas.width / (30+offset)); i++) {
		const radius = randomInRange(10, 30);
		planets.push(new Planet(radius, `rgba(${randomInRange(140, 255)}, ${randomInRange(140, 255)}, ${randomInRange(140, 255)})`, 0, true));
	}

	// center shift -x based on length of planets
	// calculate planet positions
	for(let i = 0; i < planets.length; i++) {
		// perfectly spaced out with 50 pixels between each planet, but add the radius to the x position
		const thisPlanet = planets[i];
		const previous = planets[i - 1];

		const previousX = previous?.position.x || 0;
		const previousRadius = previous?.radius || 0;

		totalWidth += (offset + thisPlanet.radius);

		thisPlanet.position.x = previousX + previousRadius + offset + thisPlanet.radius;
		thisPlanet.position.y = canvas.height/4;
	}

	const center = (canvas.width/2);
	// shift is the amount to move to the right to make the center of the planets be in the center of the canvas
	let shift = center - totalWidth;
	// fix shift because of offset
	shift += (offset*(planets.length/2)) - offset;

	console.log(`Center: ${center}, Total Width: ${totalWidth}, Shift: ${shift}, Canvas Width: ${canvas.width}`);
	planets.forEach(planet => {
		planet.position.x += shift;
	});

	// draw center line
	/*
	ctx.beginPath();
	ctx.moveTo(center, 0);
	ctx.lineTo(center, canvas.height);
	ctx.strokeStyle = 'rgba(255, 255, 255, 0.5)';
	ctx.stroke();
	ctx.closePath();
	 */

	// draw line at 200 y
	ctx.beginPath();
	ctx.moveTo(planets[0].position.x, canvas.height/4);
	ctx.lineTo(planets[planets.length - 1].position.x, canvas.height/4);
	ctx.strokeStyle = 'rgba(255, 255, 255, 0.5)';
	ctx.stroke();
	ctx.closePath();

	// draw the planets
	for(let i = 0; i < planets.length; i++) {
		planets[i].draw(ctx);
	}
}
</script>

<template>
	<Header/>
	<aside :class="['sidebar', sidebarOpen ? 'open' : 'closed']">
		<div class="flex flex-row-nowrap gap-8 align-center justify-space-between">
			<h2>Kragnix Station</h2>
			<button class="button button-positive">Travel</button>
		</div>

		<div>
			<h3>Exports</h3>
			<ul>
				<li>Hydrogen</li>
				<li>Water</li>
				<li>Oxygen</li>
				<li>Argon</li>
			</ul>
		</div>

		<br/>
		<div>
			<h3>Needs</h3>
			<ul>
				<li>Food</li>
				<li>Composites</li>
				<li>Metals</li>
				<li>Laborers</li>
				<li>Advanced Toiletries</li>
			</ul>
		</div>

		<br/>
		<div>
			<h3>Contracts</h3>
			<ul class="sidebar_contracts">
				<li>Feed the hungry</li>
				<li>Rescue the Lunoids</li>
				<li>Data Packets</li>
				<li>General Labor</li>
				<li>Passenger Transport</li>
				<li>Military Surplus</li>
			</ul>
		</div>
	</aside>
	<canvas class="game"></canvas>
</template>

<style lang="less" scoped>
.game {
	display: flex;
	width: 100%;
	height: ~"calc(100vh - 48px)";

	image-rendering: optimizeQuality;
}

.sidebar {
	position: fixed;
	left: 0;
	top: 48px;
	bottom: 0;
	width: 400px;
	background: var(--primary-dark);
	backdrop-filter: blur(8px);

	padding: 8px;
	overflow-y: auto;

	z-index: 99;

	&.closed {
		display: none;
		visibility: hidden;
		appearance: none;
	}

	h2 {
		margin: 0;
	}

	ul {
		list-style-type: circle;
		margin-left: 16px;
	}

	&_contracts {
		list-style-type: none !important;
		margin-left: 0 !important;
		li {
			padding: 8px;
			border-radius: 8px;
			background-color: var(--primary);
			color: #fff;
			margin-bottom: 8px;
			cursor: pointer;
			transition: background-color ease 0.3s;
			&:hover {
				background-color: var(--primary-dark);
			}
		}
	}
}
</style>
