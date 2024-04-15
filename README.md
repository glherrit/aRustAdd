# Gary Herrit, 15 April 2024

### using VS Code on Windows 11 laptop

### Prerequisites - install if needed (version used here)

```
- npm (version 9.8.1)
- rustup (version 1.77.1)
- wasm-pack (version 0.12.1)
```

### Step (1)

Start a new sveltekit/skeleton project
https://www.skeleton.dev/docs/get-started
create a bare bones sveltekit-skeleton project we will call this aaWasm

```
npm create skeleton-app@latest aRustAdd
```

### Step (2)

generate starter rust project

```
cargo new rustlib --lib
```

### Step (3)

change to rust folder and add wasm-bindgen dependency to project

```
cd rustlib
cargo add wasm-bindgen
```

### Step (4)

In the <span style="color:yellow;">cargo.toml</span> file add the following:

```
[lib]
crate-type = ["cdylib"]
```

### Step (5)

Modify the rust file, <span style="color:yellow;">lib.rs</span>, by adding this line at the top to allow communication to rust library using wasm-bindgen. We will use the example add function already coded in the file for this demo. The file should look like this:

```
use wasm_bindgen::prelude::\*;

#[wasm_bindgen]
pub fn add(left: usize, right: usize) -> usize {
  left + right
}
```

### Step (6)

manually compile the rust library

```
wasm-pack build --target web
```

### Step (7)

Modify the <span style="color:yellow;">vite.config.ts</span> file to allow Vite to serve the rust library files. Note that the fs server will provide vite with access to the rust library

```
import { purgeCss } from vite-plugin-tailwind-purgecss';
import { sveltekit } from '@sveltejs/kit/vite';
import { defineConfig } from 'vite';

export default defineConfig({
	plugins: [sveltekit(), purgeCss()],
	server: {
		fs: {
			allow: ['./rustlib/pkg']
		}
	}
});
```

### Step (8)

Add code to the top <span style="color:yellow;">+page.svelte</span> import rust add function at top of file and generate two math integer numbers

```
<script lang="ts">
	import init, { add } from '../../rustlib/pkg'
	const n1 = Math.trunc(Math.random()*10)
	const n2 = Math.trunc(Math.random()*10)
</script>
```

### Step (9)

Also add HTML code to call add function and display results in <span style="color:yellow;">+page.svelte</span>.

```
<div class="container h-full mx-auto flex justify-center items-center">
	<div class="space-y-20">
		<h1 class="h1">Let's Get Rust and Wasm Working!</h1>
		{#await init()}
			loading wasm bundle...
			<!-- dont need any of what init returns -->
		{:then _module}
			<h1 class="h1"><span>rust/wasm add: {n1} + {n2} = {add(n1, n2)}</span></h1>
		{/await}
	</div>
</div>
```

### Step (10)

time to test the program

```
npm run dev
```

### ====== Some Extra Information

Install rust using the rustup installation
https://www.rust-lang.org/tools/install
check your rust version by:

```
rustc --version
```

---
