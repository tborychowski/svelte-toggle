<div>
	<input {id} type="range" min="0" max="100" value="0" class="toggle"
		on:keydown="{onkeydown}"
		on:keypress="{onkeypress}"
		on:mouseup="{onmouseup}"
		bind:this="{el}">
</div>

<script>
import { createEventDispatcher } from 'svelte';
const dispatch = createEventDispatcher();

export let id;
export let value = false;
let el;

function setValue (val) {
	requestAnimationFrame(() => el.value = +val * 100);
}


function valueToBool () {
	let val = +el.value / 100;
	if (val !== 0 && val !== 1) {
		val = Math.round(val);
		// setValue(val);
	}
	return !!val;
}


function boolToValue (newValue) {
	const currValue = valueToBool();
	console.log(currValue, newValue);
	if (currValue !== newValue) {
		setValue(newValue);
		dispatch('change', value);
	}
}

function onmouseup () {
	const dragEndValue = valueToBool();
	if (value !== dragEndValue) value = dragEndValue;
	else value = !dragEndValue;
	boolToValue(value);
}


function onkeydown (e) {
	const right = e.key === 'ArrowRight';
	const left = e.key === 'ArrowLeft';
	if (left || right) {
		value = right;
		boolToValue(value);
	}
}

function onkeypress (e) {
	if (e.key === ' ' || e.key === 'Enter') {
		value = !value;
		boolToValue(value);
	}
}

</script>
