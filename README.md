# vue-image-select-areas

Component that loads an image and allows you to draw rectangle areas on it.

Uses interact.js for resizing and moving areas around.

## Props

| Name | Default | Required | Comment |
| -- | -- | -- | -- |
| `url` | | yes |  |
| `value` | `[]` | no | - |
| `width` | `100%` | no | - |
| `height` | `100%` | no | - |

## Events

| Name | Data |
| -- | -- |
| `update:modelValue` | Array of selected areas |
| `added` | Object of recently added area |

### Emitted v-model data

The data emitted by `v-model` events is an array of objects, where the objects have the following properties:

| Name | Example | Comment |
| -- | -- | -- |
| `relativeWidth` (float) | `0.123` | Calculated relative width
| `relativeHeight` (float) | `0.456` | Calculated relative height
| `relativeX` (float) | `0.1` | Calculated relative X position
| `relativeY` (float) | `0.2` | Calculated relative Y position
| `width` (float) | `123.5` | Actual selection width
| `height` (float) | `39.2` | Actual selection height
| `top` (float) | `34.2` | Actual top (y) position
| `left` (float) | `40.3` | Actual left (x) position

Only `relativeWidth`, `relativeHeight`, `relativeX` and `relativeY` is required in the object when passing it _to_ the component initially as `v-model`/`:value`. They will be computed to actual values when the image is loaded.

## Slots

| Name | Injected data | Default |
| -- | -- | -- |
| `default` | `{area, index}` | none |
| `toolbar` | `{area, index}` | Depends on `removable` prop. If true, a default delete button is shown in the top right corner. |

## Example

```vue
<script setup>
const areas = [
  {
    // Only these four properties are needed to calculate 
    // and draw up existing areas
    relativeWidth: 0.123,
    relativeHeight: 0.234,
    relativeX: 0.4,
    relativeY: 0.2,
  }
];
</script>

<image-select-areas
  url="https://picsum.photos/500/800"
  v-model="areas"
/>
```

Also see demo here: [https://vue-image-select-areas.herokuapp.com/](https://vue-image-select-areas.herokuapp.com/)
