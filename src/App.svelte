
<Toggle on:change="{onchange}" />



<script>
import Toggle from './svelte-toggle';

function onchange (e) {
	console.log('changed to', e.detail);
}
</script>
