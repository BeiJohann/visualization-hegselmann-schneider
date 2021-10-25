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
    //d3.select('#container').style("border", "5px solid gray" )
    var width = 500,
        height = 300,
        minX = 0,
        maxX = 100,
        minY = 0,
        maxY = 100,
        radius = 6,
        farbe = '#cc0000'
    var data = [{x: 16, y: 80}, {x: 80, y: 20}, {x: 30, y: 94}, {x: 2, y: 10}, {x: 74, y: 33},{x: 23, y: 85},
      {x: 80, y: 90}, {x: 12, y: 40}, {x: 6, y: 72}, {x: 42, y: 42}]

    var svg = d3.select("body")
        .append("svg")
        .attr("width", width)
        .attr("height", height);

    var xScale = d3.scaleLinear()
        .domain([minX, maxX])
        .range([0, width-100])

    var yScale = d3.scaleLinear()
        .domain([minY, maxY])
        .range([height -50, 0])

    var xAxis = d3.axisBottom()
        .scale(xScale)

    var yAxis = d3.axisLeft()
        .scale(yScale);

    var g = svg.selectAll("g")
        .data(data)
        .enter()
        .append("g")
        .attr("transform", function(d, i) {
          return "translate(50,10)";
        })
        .on("mouseenter", function() {
            d3.select(this)
              .attr("r",12)

        })

    g.append("circle")
        .attr("cx", function(d, i) {
          return xScale(data[i].x);
        })
        .attr("cy", function(d, i) {
          return yScale(data[i].y);
        })
        .attr("r", 6)
        .attr("fill", farbe)

    svg.append("g")
        .attr("transform", "translate(50, 10)")
        .call(yAxis);

    var xAxisTranslate = height -40;
    svg.append("g")
        .attr("transform", "translate(50, " + xAxisTranslate  +")")
        .call(xAxis)


  }
}
</script>

<style>
/* Your CSS styling can go here */
</style>
