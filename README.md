# vue-range-sliders
Can use the slider in vue1.x and  vue2.x

[demo](https://nightcatsama.github.io/vue-range-sliders/example/)

## Install
```
    npm install vue-range-sliders
```

## Plan

- [x] Basis
- [ ] Display maximum value & minimum value 
- [ ] piecewise style
- [ ] Tooltip
- [ ] Range

## usage
Use in vue1.x

e.g:
```
<template>
    <div>
        <vue-range v-ref:range :min="1" :max="100" :interval="3" :value.sync="val" @callback="getValue"></vue-range>
    </div>
</template>
<script>
import { vueRange }  from 'vue-range-sliders';

new Vue({
  el: '#app',
  components: {
    vueRange
  },
  data: {
    val: 1
  }
  methods: {
    getValue(val) {
      console.log(val)
    }
  }
});
</script>
```
<br>
Use in vue2.x 
<br>Because```.sync``` are deprecated. Props are now always one-way down. So in order to realize the two-way, Can be set in the @callback 

e.g:
```
<template>
    <div>
        <vue-range ref="range" :min="1" :max="100" :val="val" @callback="getValue"></vue-range>
    </div>
</template>
<script>
//  import { vue2Range }  from 'vue-range-sliders';
import vueRange from './vue-range-sliders/vue2-range-sliders.vue'

new Vue({
  el: '#app',
  components: {
    vueRange
  },
  data: {
    val: 1
  }
  methods: {
    getValue(value) {
      this.val = value
    },
    setValue(num) {
      this.val = num
      this.$refs.range.setValue(num)
    }
  }
});
</script>
```

## Options

### Props
| Props       | Type          | Default  | Description  |
| ----------- |:--------------| ---------|--------------|
| width       | Number,String | 150      | width of the component |
| height      | Number        | 4        | height of the component |
| dotSize     | Number        | 15       | size of the sliders |
| min         | Number        | 0        | The minimum value   |
| max         | Number        | 100      | The maximum value   |
| interval    | Number        | 1        | The gap between the values |
| show        | Boolean       | true     | display of the component |
| val (vue>=2)| Number        | 1        | initial value (only vue2.x)|
| value (vue>=1)| Number      | 1        | initial value, Two-way binding please use ```.sync``` (only vue1.x)|

### Function
| Name        | Params&Type   | Require  | Description  |
| ----------- |:--------------| ---------|--------------|
| setValue    | value[Number] | true     | set value of the component |

### Events
| Name        | Params&Type   | Description  |
| ----------- |:--------------|--------------|
| callback    | value[Number] | values change when the callback function |
