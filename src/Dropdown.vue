<template>
  <div class="dropdown" v-if="options">

    <!-- Dropdown Input -->
    <input class="dropdown-input"
      :name="name"
      @focus="showOptions()"
      @blur="exit()"
      @keydown="keyMonitor"
      v-model="searchFilter"
      :disabled="disabled"
      :placeholder="placeholder"
      autocomplete="off" />

    <!-- Dropdown Menu -->
    <div class="dropdown-content"
      v-show="optionsShown">
      <div
        class="dropdown-item"
        :class="{ 'is-highlighted': index === highlightedIndex }"
        @mousedown="selectOption(option)"
        v-for="(option, index) in filteredOptions"
        :key="index">
          {{ option.name || option.id || '-' }}
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'Dropdown',
    template: 'Dropdown',
    props: {
      name: {
        type: String,
        required: false,
        default: 'dropdown',
        note: 'Input name'
      },
      options: {
        type: Array,
        required: true,
        default: [],
        note: 'Options of dropdown. An array of options with id and name',
      },
      placeholder: {
        type: String,
        required: false,
        default: 'Please select an option',
        note: 'Placeholder of dropdown'
      },
      disabled: {
        type: Boolean,
        required: false,
        default: false,
        note: 'Disable the dropdown'
      },
      maxItem: {
        type: Number,
        required: false,
        default: 6,
        note: 'Max items showing'
      }
    },
    data() {
      return {
        selected: {},
        optionsShown: false,
        searchFilter: '',
        highlightedIndex: -1
      }
    },
    created() {
      this.$emit('selected', this.selected);
    },
    computed: {
      filteredOptions() {
        const filtered = [];
        const regOption = new RegExp(this.searchFilter, 'ig');
        for (const option of this.options) {
          if (this.searchFilter.length < 1 || option.name.match(regOption)){
            if (filtered.length < this.maxItem) filtered.push(option);
          }
        }
        return filtered;
      }
    },
    methods: {
      selectOption(option) {
        this.selected = option;
        this.optionsShown = false;
        this.searchFilter = this.selected.name || this.selected.id || "";
        
        // Update highlighted index
        const idx = this.filteredOptions.findIndex(o => o === option || o.id === option.id);
        this.highlightedIndex = idx;

        this.$emit('selected', this.selected);
      },
      showOptions() {
        if (!this.disabled) {
          this.optionsShown = true;
          // Highlight selected if exists, otherwise first
          if (this.selected && (this.selected.id || this.selected.name)) {
            const idx = this.filteredOptions.findIndex(
              o => o === this.selected || o.id === this.selected.id
            );
            this.highlightedIndex = idx >= 0 ? idx : 0;
          } else if (this.filteredOptions.length) {
            this.highlightedIndex = 0;
          } else {
            this.highlightedIndex = -1;
          }
        }
      },
      exit() {
        if (this.selected && (this.selected.name || this.selected.id)) {
          this.searchFilter = this.selected.name || this.selected.id;
        } else {
          this.searchFilter = '';
        }
        this.optionsShown = false;
        this.$emit('selected', this.selected);
      },
      // Key Monitor for Arrows, Enter, Escape
      keyMonitor(event) {
        const key = event.key;

        // Open options with arrows if closed
        if ((key === "ArrowDown" || key === "ArrowUp") && !this.optionsShown) {
          if (!this.disabled) {
            this.showOptions();
          }
        }

        if (!this.optionsShown) {
          return;
        }

        if (key === "ArrowDown" || key === "ArrowUp") {
          event.preventDefault();

          if (!this.filteredOptions.length) return;

          const max = this.filteredOptions.length;
          let idx = this.highlightedIndex;

          if (key === "ArrowDown") {
            if (idx === -1) {
              idx = 0;
            } else {
              idx = (idx + 1) % max;
            }
          } else if (key === "ArrowUp") {
            if (idx === -1) {
              idx = max - 1;
            } else {
              idx = (idx - 1 + max) % max;
            }
          }

          this.highlightedIndex = idx;
          return;
        }

        if (key === "Enter") {
          if (this.optionsShown && this.highlightedIndex >= 0 && this.filteredOptions[this.highlightedIndex]) {
            event.preventDefault();
            this.selectOption(this.filteredOptions[this.highlightedIndex]);
          }
          return;
        }

        if (key === "Tab") {
          if (this.optionsShown && this.highlightedIndex >= 0 && this.filteredOptions[this.highlightedIndex]) {
            this.selectOption(this.filteredOptions[this.highlightedIndex]);
          }
          // Do not return or preventDefault, allow tab to move focus
        }

        if (key === "Escape") {
          this.exit();
          return;
        }
      }
    },
    watch: {
      searchFilter() {
        if (this.filteredOptions.length === 0) {
          this.selected = {};
          this.highlightedIndex = -1;
        } else {
          if (this.searchFilter.length > 0) {
            this.selected = this.filteredOptions[0];
            this.highlightedIndex = 0;
          } else {
            this.selected = {};
            this.highlightedIndex = -1;
          }
        }
        this.$emit('filter', this.searchFilter);
      }
    }
  };
</script>


<style lang="scss" scoped>
  .dropdown {
    position: relative;
    display: block;
    margin: auto;
    .dropdown-input {
      background: #fff;
      cursor: pointer;
      border: 1px solid #e7ecf5;
      border-radius: 3px;
      color: #333;
      display: block;
      font-size: .8em;
      padding: 6px;
      min-width: 250px;
      max-width: 250px;
      &:hover {
        background: #f8f8fa;
      }
    }
    .dropdown-content {
      position: absolute;
      background-color: #fff;
      min-width: 248px;
      max-width: 248px;
      max-height: 248px;
      border: 1px solid #e7ecf5;
      box-shadow: 0px -8px 34px 0px rgba(0,0,0,0.05);
      overflow: auto;
      z-index: 1;
      .dropdown-item {
        color: black;
        font-size: .7em;
        line-height: 1em;
        padding: 8px;
        text-decoration: none;
        display: block;
        cursor: pointer;
        &:hover {
          background-color: #e7ecf5;
        }
        &.is-highlighted {
          background-color: #e7ecf5;
        }
      }
    }
    .dropdown:hover .dropdowncontent {
      display: block;
    }
  }
</style>
