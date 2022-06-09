# postcss-prune-var

PostCSS plugin to remove all unused variables in a CSS file.

A `--variable` is considered unused if it, or any other variables consuming it are not accessed by a single `var()` statement in the whole CSS file.

This is unsafe if there is a possibility of a second CSS file accessing variables from the first one.

#### How does it work, and how is it different from postcss-unused-var?

-   `postcss-unused-var` is outdated, deprecated, and didn't work right.
-   Removes variables from everywhere, including `:root`.
-   Treats all variables as global, because they are.
-   Checks and prevents removal of variables used only by other variables by following variable dependency graph.
-   At the same time, if a variable is only used by other variables, it will be removed along with those that depend on it.
-   Way faster.

## Install

```
npm install postcss-prune-var --save-dev
```

## Usage

```js
const pruneVar = require('postcss-prune-var');

const yourConfig = {
	plugins: [pruneVar()],
};
```

## Example

Input:

```css
:root {
	--root-unused: red;
	--root-unused-proxy: var(--root-unused);
	--root-used: blue;
}

.foo {
	--unused: red;
	--unused-proxy: var(--unused);
	--proxied: pink;
	--proxy: var(--proxied);
	--used: green;
	color: var(--root-used);
	background: linear-gradient(to bottom, var(--used), var(--proxy));
}
```

Output

```css
:root {
	--root-used: blue;
}

.foo {
	--proxied: pink;
	--proxy: var(--proxied);
	--used: green;
	color: var(--root-used);
	background: linear-gradient(to bottom, var(--used), var(--proxy));
}
```
