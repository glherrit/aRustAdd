<script lang="ts">
	import init, { add } from '../../rustlib/pkg'
	let n1 = Math.trunc(Math.random()*10)
	let n2 = Math.trunc(Math.random()*10)
</script>

<div class="container h-full mt-10 mx-auto flex justify-center items-center">
	<div class="space-y-20">
		<h1 class="h1">Let's Get Rust & Wasm Working!</h1>
		{#await init()}
			loading wasm bundle...
			<!-- dont need any of what init returns -->
		{:then _module}
			<h1 class="h1"><span>rust addition: {n1} + {n2} = {add(n1, n2)}</span></h1>
		{/await}
	</div>
</div>
