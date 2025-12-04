# vue-simple-search-dropdown (Enhanced)

> [!NOTE]
> This project is a fork of [vue-simple-search-dropdown](https://github.com/romainsimon/vue-simple-search-dropdown) by Romain Simon. It includes additional features like keyboard navigation (arrows, tab, enter), improved selection handling, and bug fixes for empty input states.

> [!WARNING]
> **Compatibility Note:** This component is designed for **Vue 2** only. It is **NOT** compatible with Vue 3.

A Vue 2 component for a simple searchable dropdown.

No external library is used in this dropdown.

### Demo

Demo here: [https://purrkeet.github.io/vue-simple-search-dropdown/](https://purrkeet.github.io/vue-simple-search-dropdown/)

## Installation

```js
npm install vue-simple-search-dropdown-enhanced
```

### Browser

Include the script file, then install the component with `Vue.use(Dropdown);` e.g.:

```html
<script type="text/javascript" src="node_modules/vuejs/dist/vue.min.js"></script>
<script type="text/javascript" src="node_modules/vue-simple-search-dropdown/dist/vue-simple-search-dropdown.min.js"></script>
<script type="text/javascript">
  Vue.use(Dropdown);
</script>
```

### Module

```js
import Dropdown from 'vue-simple-search-dropdown-enhanced';
```

## Usage

Once installed, it can be used in a template as simply as:

```html
<Dropdown
    :options="[{ id: 1, name: 'Option 1'}, { id: 2, name: 'Option 2'}]"
    v-on:selected="validateSelection"
    v-on:filter="getDropdownValues"
    :disabled="false"
    name="zipcode"
    :maxItem="10"
    placeholder="Please select an option">
</Dropdown>
```

### Options

- `options` (required): An array of options with `id` and `name`
- `placeholder` (optional): A placeholder 
- `disabled` (optional): true/false
- `name` (optional): An input name | default: `dropdown`
- `maxItem` (optional): Max item to show | default: `6`

### Events

These events are returned from the dropdown and can be catch with `v-on`
- `selected`: An option is selected by click in the dropdown
- `filter`: A filter has been applied by typing in the input field

Tips: Using `v-on:filter`, you can repopulate the dropdown with new `options` corresponding to the search by making an API call
