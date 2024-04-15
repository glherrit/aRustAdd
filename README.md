# Gary Herrit 4 April 2024

### using VS Code on Windows 11 laptop

### Prerequisites - install if needed (version used here)

- npm (version 9.8.1)
- rustup (version 1.77.1)
- wasm-pack (version 0.12.1)

### Step (1)

Start a new sveltekit/skeleton project
https://www.skeleton.dev/docs/get-started
create a bare bones sveltekit-skeleton project we will call this aaWasm

**npm create skeleton-app@latest aRustAdd**

### Step (2)

generate starter rust project

**cargo new rustlib --lib**

### Step (3)

change to rust folder and add wasm-bindgen dependency

**cd rustlib**
**cargo add wasm-bindgen**

### Step (4)

in cargo.toml file add the following:

**[lib]**
**crate-type = ["cdylib"]**

### Step (5)

modify the rust file, lib.rs
Add this line to top to allow
communication to rust library using wasm-bindgen
use the example add function for this purpose
the file should look like this:

**use wasm_bindgen::prelude::\*;**

#[wasm_bindgen]
pub fn add(left: usize, right: usize) -> usize {
left + right
}

### Step (6)

manually compile the rust library

**wasm-pack build --target web**

// Step (7)
// Modify the vite.config.ts file to allow
// Vite to serve the rust library files.
// Note that the fs server will provide vite
// with access to the rust library
export default defineConfig({
plugins: [sveltekit(), purgeCss()],
server: {
fs: {
allow: ['./rustlib/pkg']
}
}
});

// Step (8)
// Add code to +page.svelte import rust add function
// at top of file add line

<script lang="ts">
	import init, { add } from '../../rustlib/pkg'
	const n1 = Math.trunc(Math.random()*10)
	const n2 = Math.trunc(Math.random()*10)
</script>

// Step (9)
// Add HTML code to call add function and display results

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

// Step (10)
// time to test the program
npm run dev

// Install rust using the rustup installation
// https://www.rust-lang.org/tools/install
// check your rust version by:
// rustc --version

---

cargo new sum --lib
cd sum
cargo add wasm-bindgen

**_ in lib.rs _** add
use wasm_bindgen::prelude::\*;

#[wasm_bindgen]
pub fn sum(numbers: &[i32]) -> i32 {
numbers.iter().sum()
}

\*\*\* add to cargo.toml
[lib]
crate-type = ["cdylib"]

[profile.release]
lto = true
strip = true

cargo build --release --bin ab --target wasm32-unknown-unknown
cargo build --release --bin sum --target wasm32-unknown-unknown

wasm-pack build --target web

**\*\*\*\***\*\*\***\*\*\*\*** not necessary VVVV

npm i -D vite-plugin-wasm

npm i -D vite-plugin-top-level-await

\*\*\* modify vit.config.ts
import wasm from 'vite-plugin-wasm';
import topLevelAwait from 'vite-plugin-top-level-await';

export default defineConfig({
plugins: [wasm(), topLevelAwait(), sveltekit(), purgeCss()]
});
