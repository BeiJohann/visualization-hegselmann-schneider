<template>
  <div id="dummy-component-wrapper">
    Please make sure, that there is only ONE parent element (here the div with id="wrapper") which contains all child elements.
    <br><br>
    {{someValue}}
  </div>
</template>

<script>
import * as d3 from 'd3'

export default {
  props: {
    someValue: { type: String }
  },
  mounted() {
    // your code goes here
  }
}
</script>

