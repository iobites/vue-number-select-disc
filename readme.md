# Vue Number Select Disc

> Vue Component

This component is in an early development state.  

-> [Demo](https://iobites.github.io/vue-number-select-disc/)

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
<vue-number-select-disc v-model="myValue" />
```                          

### Available attributes
| Name | Type | Default | Description |
| --- | --- | --- | --- |
| width | [Number, String] | 200 | Component width |
| height | [Number, String] | 200 |  Component height |
| v-model | [Number, String] | 0 | Value binding |
| min | Number | 0 | Allowed min value |
| max | Number | 10000 | Allowed max value |
| precision | Number | 1 | Adjust how amount of degree needed to inc or dec the value |
| disabled | Boolean | false | Component will be inert |
| signed | Boolean | false | Is the number signed or unsigned |
| showText | Boolean | true | Whether to show the value and unit |
| floating | Boolean | true | Floating point numbers |
| fraction | Number | 0 > x < 5 | Number fraction to show |
| stepWidth | Number | 1 | Amount to inc or dec the value |
| strokeWidth | Number | 20 | Outer circle width |
| unit | String | '' | Unit will be appended to the value text |



### Available events

| Name | Description |
| --- | --- |
| selected | Will be triggered when a new number was selected |


## License

[MIT](http://opensource.org/licenses/MIT)