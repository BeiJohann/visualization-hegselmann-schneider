<template>
  <div id="container">
    <h1>Visualization WS21/22: InfoVis</h1>
  </div>
</template>

<script>
import * as d3 from 'd3'
import './app.js'

export default {
  name: 'App',
  components: { },
  mounted() {
    // your code goes here
    let d = await d3.csv("https://corgis-edu.github.io/corgis/datasets/csv/cars/cars.csv")
    console.log(d);
    //console.log(d.getElementById("Engine Information.Engine Statistics.Horsepower"));
    let horsepower = d.map(value => value["Engine Information.Engine Statistics.Horsepower"])
    let fuel = d.map(value => value["Fuel Information.City mpg"])

    console.log(horsepower)
    console.log(fuel)
  }
}
</script>

<style>
/* Your CSS styling can go here */
</style>
