<template>
  <div id="container">
    <h1>Visualization WS21/22: InfoVis</h1>
    <div id="scatter-plot-canvas"></div>
    <div id="info"></div>
  </div>
</template>

<script>
import * as d3 from 'd3'
import './app.js'

export default {
  name: 'App',
  components: { },
  async mounted() {
    // your code goes here
    let d = await d3.csv("https://corgis-edu.github.io/corgis/datasets/csv/cars/cars.csv")
    let horsepower = d.map(value => value["Engine Information.Engine Statistics.Horsepower"])
    let fuel = d.map(value => value["Fuel Information.City mpg"])
    let data = horsepower.map(function (value, index){
      return [value, fuel[index]]
    })
    console.log(data)

    let w = 900
    let h = 500
    let margin = { top: 80, right: 80, bottom: 40, left: 40 }
    let radius = 6
    let color1 = '#cc0000'
    let color2 = '#00cc00'
    let domainX = [Math.min(...horsepower), Math.max(...horsepower)]
    let domainY = [Math.min(...fuel), Math.max(...fuel)]

    let svg = d3.select('#scatter-plot-canvas')
        .append('svg')
        .attr('width', w)
        .attr('height', h)

    // create x scale + axis
    let xScale = d3.scaleLinear()
        .domain(domainX)
        .range([margin.left, w - margin.right])

    let xAxis = d3.axisBottom().scale(xScale)
    svg.append("g")
        .attr('transform', `translate(0, ${h - margin.bottom})`)
        .call(xAxis)
    svg.append("text")
        .attr("transform", `translate(${w / 2}, ${h})`)
        .style("text-anchor", "middle")
        .text("x")

    // crate y scale + axis
    let yScale = d3.scaleLinear()
        .domain(domainY)
        .range([h - margin.bottom, margin.top])

    let yAxis = d3.axisLeft().scale(yScale)
    svg.append("g")
        .attr('transform', `translate(${margin.left}, 0)`)
        .call(yAxis)
    svg.append("text")
        .attr("transform", `rotate(-90)`)
        .attr("x", - (h / 2))
        .attr("y", 0)
        .attr("dy", "1em")
        .style("text-anchor", "middle")
        .text("y")

    let dataset = [
      {
        x: 0,
        y: 0
      },
      {
        x: domainX[1],
        y: domainY[1],
      }
    ]
    console.log(dataset)

    for(let i = 0; i< 8; i++) {
      dataset.push({
        x: Math.random() * domainX[1],
        y: Math.random() * domainY[1]
      })
    }

    function onMouseOver(e, d) {
      d3.select(this)
          .attr("fill", color2)
          .attr("r", radius * 2)
      d3.select("#info").html(`(${d[0]}, ${d[1]})`)
    }

    function onMouseLeave() {
      d3.select(this)
          .attr("fill", color1)
          .attr("r", radius)
      d3.select("#info").html(``)
    }

    function onMouseClick(e) {
      const pos = d3.pointer(e)
      const scaledPos = {
        x: xScale.invert(pos[0]),
        y: yScale.invert(pos[1])
      }
      dataset.push(scaledPos)
      drawPoints()
    }

    function drawPoints() {
      svg.selectAll('circle')
          .remove()

      svg.append('g')
          .selectAll('circle')
          .data(data)
          .enter()
          .append("circle")
          .attr("cx", function(d) {
            return xScale(d[0])
          })
          .attr("cy", d => {
            return yScale(d[1])
          })
          .attr("r", radius)
          .attr("fill", color1)
          .on("mouseover", onMouseOver)
          .on("mouseleave", onMouseLeave)
    }

    svg.on("click", onMouseClick)
    drawPoints()

    //Histogram-----------------------------------------------------------------------------
    // Heavily inspired from: https://bl.ocks.org/larsenmtl/b00fcedde4d3d37098509f514a354bac
    var gTop = svg.append("g")

    var xBins = d3.histogram()
        .domain(xScale.domain())
        .thresholds(xScale.ticks(10))
        .value(function(d) {
          return d[0];
        })(data);

    var xy = d3.scaleLinear()
        .domain([0, d3.max(xBins, function(d) {
          return d.length;
        })])
        .range([margin.top, 0]);

    var xBar = gTop.selectAll(".bar")
        .data(xBins)
        .enter().append("g")
        .attr("class", "bar")
        .attr("transform", function(d) {
          return "translate(" + xScale(d.x0) + "," + xy(d.length) + ")";
        });

    var bWidth = xScale(xBins[0].x1) - xScale(xBins[0].x0) - 1;
    xBar.append("rect")
        .attr("x", 1)
        .attr("width", bWidth)
        .attr("height", function(d) {
          return margin.top - xy(d.length);
        })
        .style("fill", color1);

    xBar.append("text")
        .attr("dy", ".75em")
        .attr("y", 2)
        .attr("x", bWidth / 2)
        .attr("text-anchor", "middle")
        .text(function(d) {
          return d.length;
        })
        .style("fill", "black")
        .style("font", "9px sans-serif");

    // right histogram----------------------------------------------------------------------------------
    var gRight = svg.append("g")
        .attr("transform",
            "translate(" + (w - margin.right) + "," + 0 + ")");

    var yBins = d3.histogram()
        .domain(yScale.domain())
        .thresholds(yScale.ticks(10))
        .value(function(d) {
          return d[1];
        })(data);

    var yx = d3.scaleLinear()
        .domain([0, d3.max(yBins, function(d) {
          return d.length;
        })])
        .range([0, margin.right]);

    var yBar = gRight.selectAll(".bar")
        .data(yBins)
        .enter().append("g")
        .attr("class", "bar")
        .attr("transform", function(d) {
          return "translate(" + 0 + "," + yScale(d.x1) + ")";
        });

    var ybWidth = yScale(yBins[0].x0) - yScale(yBins[0].x1) - 1;
    yBar.append("rect")
        .attr("y", 1)
        .attr("width", function(d){
          return yx(d.length);
        })
        .attr("height", ybWidth)
        .style("fill", color1);

    yBar.append("text")
        .attr("dx", "-.75em")
        .attr("y", ybWidth / 2 + 1)
        .attr("x", function(d){
          return yx(d.length);
        })
        .attr("text-anchor", "middle")
        .text(function(d) {
          return d.length;
        })
        .style("fill", "black")
        .style("font", "9px sans-serif");
  }
}
</script>

<style>
/* Your CSS styling can go here */
</style>
