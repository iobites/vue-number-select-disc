# Vue Number Select Disc

> Vue Component

This component is in an early development state.  

-> [Demo](https://iobites.github.io/vue-number-select-disc)

## Install

> This plugin supports Vue >= 2.0.

Available through npm as `vue-number-select-disc`.

```bash
npm install vue-number-select-disc
# or
yarn add vue-number-select-disc
```

```Javascript
import Vue from 'vue'
import VueNumberSelectDisc from 'vue-number-select-disc'

Vue.use(VueNumberSelectDisc)
```
## Usage

```Javascript
<vue-number-select-disc v-model="currentNumber"
                          @selected="numberSelected"
                          class="number-disc"
                          width="200"
                          height="200"
                          floating
                          :stepWidth="0.1"
                          :fraction="2"
                          :precision="10"
                          :strokeWidth="50"
                          :unit="cm" />
```                          

## License

[MIT](http://opensource.org/licenses/MIT)