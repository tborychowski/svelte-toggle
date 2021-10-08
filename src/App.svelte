<Toggle value="{val}" disabled /><br>
<Toggle on:change="{onchange}" value="{val}" /><br>
Value: {val}

<script>
import Toggle from './svelte-toggle';

let val = false;

function onchange (e) {
	val = e.detail;
}
</script>
