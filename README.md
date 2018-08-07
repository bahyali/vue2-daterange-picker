# vue2-daterange-picker

> Vue2 date range picker base on https://github.com/dangrossman/bootstrap-daterangepicker (no jQuery)

NOT READY FOR PRODUCTION YET

## Demo
You can see a demo here: 
https://innologica.github.io/vue2-daterange-picker/

## ADDED BY BAHY
- Better Mobile support
- Ability to Auto Apply
- Implemented button names

## Installation

``` bash
# install via npm
npm i vue2-daterange-picker --save
```

## Usage

```javascript
import DateRangePicker from 'vue2-daterange-picker/src/components/DateRangePicker'

export default {
    components: { DateRangePicker },
    data() {
        return {
            startDate: '2017-09-05',
            endDate: '2017-09-15'
        }
    }
}
```

```html
<template>
    <date-range-picker :startDate="startDate" :endDate="endDate" @update="console.log(value)"></date-range-picker>
</template>
```


## TODO

- [ ] documentation
- [ ] tests
- [ ] calendar range [show between two dates.]
- [ ] disabled dates
- [ ] make install function
- [ ] export single components


### Run local demo

For detailed explanation on how things work, checkout the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).
