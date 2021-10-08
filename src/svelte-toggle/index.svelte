<div class="toggle" class:checked="{value}" disabled="{disabled}"
	tabIndex="{disabled ? undefined : 0}"
	on:keydown="{onKey}"
	on:touchstart={dragStart}
	on:mousedown={dragStart}
	on:click|preventDefault>
	<label class="toggle-label" bind:this="{label}">
		<div class="toggle-handle" bind:this="{handle}"></div>
		<input type="checkbox" class="toggle-input" bind:checked="{value}">
	</label>
</div>

<script>
import { onMount } from 'svelte';
import { createEventDispatcher } from 'svelte';
const dispatch = createEventDispatcher();

const getMouseX = e => (e.type === 'touchmove') ? e.touches[0].clientX : e.clientX;
const getElemWidth = el => el.getBoundingClientRect().width;

export let value = false;
export let disabled;
let label, handle, startX, maxX, currentX = 0;
let isClick = false, isDragging = false;


onMount(() => {
	maxX = getElemWidth(label) - getElemWidth(handle);
	setValue();
});

function setValue (v) {
	if (typeof v !== 'undefined') value = v;
	startX = currentX = value ? maxX : 0;
	label.style.marginLeft = `${currentX}px`;
	dispatch('change', value);
}

function onKey (e) {
	if (e.key === 'Enter' || e.key === ' ') setValue(!value);
}

function dragStart (e) {
	document.addEventListener('mouseup', dragEnd);
	document.addEventListener('mousemove', drag);
	document.addEventListener('touchend', dragEnd);
	document.addEventListener('touchmove', drag);
	label.style.transition = 'none';
	startX = getMouseX(e) - currentX;
	isDragging = true;
	isClick = true;
}

function dragEnd () {
	document.removeEventListener('mouseup', dragEnd);
	document.removeEventListener('mousemove', drag);
	document.removeEventListener('touchend', dragEnd);
	document.removeEventListener('touchmove', drag);
	label.style.transition = '';
	isDragging = false;

	if (isClick) setValue(!value);
	else setValue(currentX >= (maxX / 2));
}

function drag (e) {
	if (!isDragging) return;
	isClick = false;
	e.preventDefault();
	currentX = getMouseX(e) - startX;
	if (currentX < 0) currentX = 0;
	if (currentX > maxX) currentX = maxX;
	label.style.marginLeft = `${currentX}px`;
}

</script>
