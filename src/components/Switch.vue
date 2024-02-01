<script setup lang="ts">
import { defineProps } from 'vue'

defineProps({
	label: String
})

</script>

<template>
	<label>
		{{ label }}
		<input class="toggle" type="checkbox"/>
	</label>
</template>

<style scoped>

label {
	display: flex;
	align-items: center;
	justify-content: center;
	gap: 8px;

	flex-flow: column;
}

.toggle {
	-webkit-appearance: none;
	-moz-appearance: none;
	appearance: none;
	width: 22px;
	height: 62px;
	display: inline-block;
	position: relative;
	border-radius: 50px;
	overflow: hidden;
	outline: none;
	border: none;
	cursor: pointer;
	background-color: #707070;
	transition: background-color ease 0.3s;

	&:before {
		content: "";
		display: block;
		position: absolute;
		z-index: 2;
		width: 15px;
		height: 15px;
		background: #fff;
		left: 3px;
		top: 42px;
		border-radius: 12px;
		color: #fff;
		transition: all cubic-bezier(0.3, 1.5, 0.7, 1) 0.3s;
	}

	&:checked {
		background-color: var(--assertive);

		&:before {
			top: 4px;
		}
	}
}
</style>