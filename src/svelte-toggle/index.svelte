<div class="toggle" class:checked="{value}"
	tabIndex="-1"
	on:touchstart={dragStart}
	on:mousedown={dragStart}
	on:click="{onclick}">
	<label class="toggle-label" bind:this="{label}">
		<div class="toggle-handle" bind:this="{handle}"></div>
		<input type="checkbox" class="toggle-input" bind:checked="{value}">
	</label>
</div>

<script>
import { onMount } from 'svelte';
import { createEventDispatcher } from 'svelte';
const dispatch = createEventDispatcher();

export let value = false;
let label, handle;
let initialX, currentX, isClick = false, active = false;
let maxOffset = 38, xOffset = 0;
let trans;

onMount(() => {
	const labelWidth = label.getBoundingClientRect().width;
	const handleWidth = handle.getBoundingClientRect().width;
	const handleCSS = getComputedStyle(handle);
	const handleMargins = parseFloat(handleCSS.marginLeft) + parseFloat(handleCSS.marginRight);
	maxOffset = labelWidth - handleWidth - handleMargins;
	xOffset = value ? maxOffset : 0;
	label.style.marginLeft = `${xOffset}px`;
});

function onclick (e) {
	e.preventDefault();
}

function dragStart (e) {
	active = true;
	isClick = true;
	document.addEventListener('mouseup', dragEnd);
	document.addEventListener('mousemove', drag);
	document.addEventListener('touchend', dragEnd);
	document.addEventListener('touchmove', drag);
	trans = getComputedStyle(label).transition;
	label.style.transition = 'none';

	if (e.type === 'touchstart') initialX = e.touches[0].clientX - xOffset;
	else initialX = e.clientX - xOffset;
}

function dragEnd () {
	document.removeEventListener('mouseup', dragEnd);
	document.removeEventListener('mousemove', drag);
	document.removeEventListener('touchend', dragEnd);
	document.removeEventListener('touchmove', drag);

	if (isClick) xOffset = (xOffset === maxOffset) ? 0 : maxOffset;
	else xOffset = (xOffset < (maxOffset / 2)) ? 0 : maxOffset;

	label.style.marginLeft = `${xOffset}px`;
	label.style.transition = trans;
	initialX = xOffset;
	active = false;

	value = (xOffset === 0);
	dispatch('change', value);
}


function drag (e) {
	if (!active) return;
	isClick = false;
	e.preventDefault();
	if (e.type === 'touchmove') currentX = e.touches[0].clientX - initialX;
	else currentX = e.clientX - initialX;
	xOffset = currentX;
	if (xOffset < 0) xOffset = 0;
	if (xOffset > maxOffset) xOffset = maxOffset;

	label.style.marginLeft = `${xOffset}px`;
}

</script>
