<template>
  <div>
    <div :class="['app-input', {'app-input--expanded': expanded}]" @click="sheet = !sheet">
      <div class="app-input__label" @click="$refs.search.focus()">
        {{label}}
      </div>
      <div class="app-input__input">
        <span v-if="optionsValue">
          {{optionsValue}}
        </span>
        <span v-else class="app-input__placeholder">
          {{placeholder}}
        </span>
      </div>
    </div>
    <transition name="fade">
      <div class="overlay" v-if="sheet"></div>
    </transition>
    <div :class="['sheet', {'sheet--visible': sheet}]">
      <div class="sheet__header">
        <input ref="search" class="sheet__header__input" :placeholder="label" :value="search" @input="search = $event.target.value">
        <div class="sheet__header__button" @click="clear">
          <AppIcon icon="x-mark-circle-thin-svg"></AppIcon>
        </div>
      </div>
      <div class="sheet__item" @click="submit(option[valueField])" v-for="(option, index) in optionsFiltered" :key="index">
        {{option[keyField]}}
      </div>
    </div>
  </div>
</template>

<style scoped>
  .app-input { box-sizing: border-box; justify-content: space-between; display: flex; width: 100%; border-radius: 5px; padding: 10px; transition: background-color .25s, color .25s; }
  .app-input__label, .app-input__input { width: 50%; }
  .app-input__label { overflow-x: hidden; }
  .app-input__input { text-align: right; background: white; transition: background-color .25s, color .25s; }
  .app-input--expanded { background: rgb(255, 253, 112); color: rgb(43, 32, 0); }
  .app-input__placeholder { color: rgba(0,0,0,.35); }
  .app-input:active, .app-input:active .app-input__input { background: rgb(255, 253, 112); }

  .overlay { z-index: 400; background: rgba(0,0,0,1); top: 0; left: 0; bottom: 0; right: 0; position: fixed; }

  .fade-enter-active, .fade-leave-active { transition: opacity .75s; }
  .fade-enter, .fade-leave-to { opacity: 0; }
  .fade-enter-to, .fade-leave { opacity: .5; }

  .sheet { z-index: 500; overflow-y: scroll; transition: transform .75s cubic-bezier(0.55, 0, 0.1, 1); transform: translateY(120vh); left: 0; right: 0; background: white; bottom: 0; position: fixed; top: 0; }
  .sheet--visible { transform: translateY(0); }
  .sheet__header { display: flex; align-items: center; padding: 10px; text-align: right; }
  .sheet__header__input { box-sizing: border-box; width: 100%; padding: 10px; border-radius: 3px; border: 1px solid rgba(0,0,0,.2); }
  .sheet__header__button { font-size: 1.25em; padding: 10px; }
  .sheet__item { border-radius: 5px; margin: 5px 10px; padding: 10px; }
  .sheet__item:active { background: rgb(255, 253, 112); }
</style>

<script>
  import { find, filter } from "lodash"
  import AppIcon from "./AppIcon.vue"

  export default {
    components: {
      AppIcon,
    },
    props: [ 'value', 'label', 'placeholder', 'options', 'key-field', 'value-field', ],
    data: function() {
      return {
        expanded: null,
        sheet: null,
        search: null,
      }
    },
    computed: {
      optionsValue() {
        let res = find(this.options, (object) => {
          if (object[this.valueField])
            return object[this.valueField].toLowerCase() == this.value.toLowerCase()
        })
        if (res)
          return res[[this.keyField]]
      },
      optionsFiltered() {
        return filter(this.options, (option) => {
          return option[this.keyField].toLowerCase().indexOf((this.search || '').toLowerCase()) != -1
        })
      },
    },
    methods: {
      valueUpdate(e) {
        this.$emit('input', e.target.value);
      },
      submit(value) {
        this.$emit('input', value)
        this.sheet = null
      },
      clear() {
        if (this.search) {
          this.search = null
          if (this.$refs.search)
            this.$refs.search.focus()
        } else {
          this.sheet = null
        }
      }
    },
  }
</script>
