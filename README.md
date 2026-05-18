<p align="center">
  <img src="assets/pc-parallax-logo.svg" alt="pc-parallax logo" width="320">
</p>

# pc-parallax

A small reusable CSS-only parallax component using native CSS Scroll-driven Animations.

No JavaScript is required.

## Features

- CSS-only
- No JavaScript
- Multiple parallax layers
- Shared section timeline
- Per-layer travel distance
- Per-layer direction
- Per-layer stacking order
- Reduced-motion fallback
- Scoped component CSS with `@layer` and `@scope`
- Typed custom properties with `@property`

## File structure

```text
pc-parallax/
├─ README.md
├─ LICENSE
├─ assets/
│  └─ pc-parallax-logo.svg
├─ src/
│  └─ pc-parallax.css
└─ demo/
   ├─ index.htm
   └─ demo.css
```

## Basic usage

Include the component CSS:

```html
<link rel="stylesheet" href="src/pc-parallax.css">
```

Create a parallax section:

```html
<section class="pc-parallax" style="--pc-height: 90vh;">
  <div class="pc-parallax__frame">
    <div
      class="pc-parallax__layer"
      style="--pc-travel: 20vh; --pc-z: 1;"
      aria-hidden="true"
    >
      <!-- Decorative background layer -->
    </div>

    <div
      class="pc-parallax__layer"
      data-direction="down"
      style="--pc-travel: 30vh; --pc-z: 2;"
    >
      <!-- Foreground content -->
    </div>
  </div>
</section>
```

## Required markup

A parallax section needs this structure:

```html
<section class="pc-parallax">
  <div class="pc-parallax__frame">
    <div class="pc-parallax__layer">
      ...
    </div>
  </div>
</section>
```

The component uses normal HTML. You can put images, SVG, text, cards, or other HTML inside a layer.

## API

### `.pc-parallax`

The outer component.

| Variable | Type | Default | Description |
|---|---:|---:|---|
| `--pc-height` | length or percentage | `85vh` | Visible height of the parallax section |
| `--pc-background` | background value | `transparent` | Background behind all layers |

Example:

```html
<section
  class="pc-parallax"
  style="--pc-height: 90vh; --pc-background: #102033;"
>
  ...
</section>
```

### `.pc-parallax__layer`

A moving layer inside the section.

| Variable / attribute | Type | Default | Description |
|---|---:|---:|---|
| `--pc-travel` | length or percentage | `20vh` | Total movement distance |
| `--pc-z` | integer | `0` | Layer stacking order |
| `data-direction="down"` | attribute | absent | Reverses movement direction |

Default direction is upward.

Example:

```html
<div
  class="pc-parallax__layer"
  style="--pc-travel: 26vh; --pc-z: 3;"
>
  ...
</div>
```

Downward movement:

```html
<div
  class="pc-parallax__layer"
  data-direction="down"
  style="--pc-travel: 34vh; --pc-z: 2;"
>
  ...
</div>
```

## How movement works

`--pc-travel` defines how far a layer moves while the `.pc-parallax` section passes through the viewport.

Example:

```css
--pc-travel: 26vh;
```

This means:

> The layer moves by 26% of the viewport height during the section's scroll passage.

Default movement is upward.

```html
<div class="pc-parallax__layer" style="--pc-travel: 26vh;">
```

Downward movement:

```html
<div
  class="pc-parallax__layer"
  data-direction="down"
  style="--pc-travel: 26vh;"
>
```

## Recommended values

These are only starting points.

| Layer type | Suggested travel |
|---|---:|
| Far background | `5vh` to `15vh` |
| Middle layer | `15vh` to `30vh` |
| Foreground | `25vh` to `45vh` |
| Text | `5vh` to `20vh` |

Avoid excessive values unless the layer has enough visual reserve. Strong movement can move content out of the visible frame.

## Accessibility

Decorative layers should be hidden from assistive technology:

```html
<div
  class="pc-parallax__layer"
  style="--pc-travel: 20vh; --pc-z: 1;"
  aria-hidden="true"
>
  ...
</div>
```

Do not hide meaningful content:

```html
<div
  class="pc-parallax__layer"
  style="--pc-travel: 12vh; --pc-z: 5;"
>
  <h2>Visible content</h2>
  <p>This text is part of the page content.</p>
</div>
```

The component includes a reduced-motion fallback:

```css
@media (prefers-reduced-motion: reduce) {
  .pc-parallax__layer {
    animation: none;
    transform: translateY(0%);
  }
}
```

## Styling

The component CSS does not define optical styles such as shadows, border radius, colors, or typography.

You can style the frame from project CSS:

```css
.my-parallax .pc-parallax__frame {
  border-radius: 2rem;
  box-shadow: 0 2rem 5rem rgb(0 0 0 / 0.35);
}
```

You can style layer content normally:

```css
.my-parallax-text {
  color: white;
  text-shadow: 0 0.25rem 1.2rem rgb(0 0 0 / 0.7);
}
```

## Why `@property` is used

The component registers the functional custom properties:

```css
@property --pc-height {
  syntax: "<length-percentage>";
  inherits: false;
  initial-value: 85vh;
}

@property --pc-travel {
  syntax: "<length-percentage>";
  inherits: false;
  initial-value: 20vh;
}

@property --pc-z {
  syntax: "<integer>";
  inherits: false;
  initial-value: 0;
}
```

This gives the values a type and a stable fallback if invalid values are used.

Invalid examples:

```css
--pc-travel: fast;
--pc-height: large;
--pc-z: front;
```

These are invalid for the registered syntax and fall back to the initial value.

`@property` does not validate design intent. It cannot enforce rules such as:

```text
--pc-travel must be below 50vh
--pc-z must be between 1 and 10
```

Those limits should be documented and tested per project.

## Why `@scope` is used

The component uses:

```css
@layer parallax-component {
  @scope (.pc-parallax) {
    ...
  }
}
```

This keeps component selectors limited to one `.pc-parallax` instance.

The component still uses prefixed class names because `@scope` is not a replacement for naming discipline.

## Why no Web Component?

This project intentionally avoids JavaScript.

A JavaScript Web Component could provide a nicer attribute API, such as:

```html
<pc-parallax-layer travel="20vh" z="1">
```

But then the component depends on JavaScript.

This component uses plain HTML and CSS instead:

```html
<div
  class="pc-parallax__layer"
  style="--pc-travel: 20vh; --pc-z: 1;"
>
```

That is less elegant, but it is more robust when the goal is a CSS-only solution.

## Running the demo

Open `demo/index.htm` in a browser.

If relative CSS loading behaves differently in your environment, serve the repository root locally, for example:

```bash
python -m http.server 8080
```

Then open:

```text
http://localhost:8080/demo/
```

## Production checklist

Before using this in production:

- Replace demo graphics with real images or project content.
- Keep decorative layers `aria-hidden="true"`.
- Keep meaningful text visible to assistive technology.
- Test values on mobile and desktop.
- Keep `--pc-travel` moderate for text layers.
- Avoid too many large image or video layers for performance.
- Use project CSS for visual styling, not the component core.

## License

MIT. See [LICENSE](LICENSE).
