<template>
  <div id="container">
    <h1>Visualization WS21/22: InfoVis</h1>
    <div id="my_dataviz"></div>
  </div>
</template>

<script>
import * as d3 from 'd3'
import './app.js'

export default {
  name: 'App',
  components: { },
  mounted() {
    // data
    function Entry(group, variable, value) {
      this.group = group;
      this.variable = variable;
      this.value = value;
    }

    let data = [new Entry("Mercedes 300 SEL", "Hubraum", "6332"),
      new Entry("Mercedes 300 SEL", "Max. Geschwi.", 220),
      new Entry("Mercedes 300 SEL", "Leistung", 250),
      new Entry("Mercedes 300 SEL", "Preis", 40000),
      new Entry("Renault R8", "Hubraum", "1108"),
      new Entry("Renault R8", "Max. Geschwi.", 133),
      new Entry("Renault R8", "Leistung", 43),
      new Entry("Renault R8", "Preis", 5400),
      new Entry("Alfa Romeo 1750", "Hubraum", 1779),
      new Entry("Alfa Romeo 1750", "Max. Geschwi.", 180),
      new Entry("Alfa Romeo 1750", "Leistung", 115),
      new Entry("Alfa Romeo 1750", "Preis", 11500),
      new Entry("Cadillac 68", "Hubraum", 7030),
      new Entry("Cadillac 68", "Max. Geschwi.", 190),
      new Entry("Cadillac 68", "Leistung", 340),
      new Entry("Cadillac 68", "Preis", 35000),
      new Entry("BMW 2000", "Hubraum", 1990),
      new Entry("BMW 2000", "Max. Geschwi.", 168),
      new Entry("BMW 2000", "Leistung", 100),
      new Entry("BMW 2000", "Preis", 11500)]

    console.log(data)

    // Hab mich hier dran orientiert: https://www.d3-graph-gallery.com/graph/heatmap_style.html

    // set the dimensions and margins of the graph
    const margin = {top: 80, right: 25, bottom: 30, left: 80},
        width = 600 - margin.left - margin.right,
        height = 450 - margin.top - margin.bottom;

// append the svg object to the body of the page
    const svg = d3.select("#my_dataviz")
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform", `translate(${margin.left}, ${margin.top})`);


      // Labels of row and columns -> unique identifier of the column called 'group' and 'variable'
    const myGroups = Array.from(new Set(data.map(d => d.group)))
    const myVars = Array.from(new Set(data.map(d => d.variable)))

    // Build X scales and axis:
    const x = d3.scaleBand()
        .range([ 0, width ])
        .domain(myGroups)
        .padding(0.05);
    svg.append("g")
        .style("font-size", 15)
        .attr("transform", `translate(0, ${height})`)
        .call(d3.axisBottom(x).tickSize(0))
        .select(".domain").remove()

    // Build Y scales and axis:
    const y = d3.scaleBand()
        .range([ height, 0 ])
        .domain(myVars)
        .padding(0.05);
    svg.append("g")
        .style("font-size", 15)
        .call(d3.axisLeft(y).tickSize(0))
        .select(".domain").remove()

    // Build color scale
    const myColorH = d3.scaleSequential()
        .interpolator(d3.interpolateInferno)
        .domain([1000,8000])

    const myColorM = d3.scaleSequential()
        .interpolator(d3.interpolateInferno)
        .domain([130,250])

    const myColorL = d3.scaleSequential()
        .interpolator(d3.interpolateInferno)
        .domain([40,350])

    const myColorP = d3.scaleSequential()
        .interpolator(d3.interpolateInferno)
        .domain([5000,45000])

    // create a tooltip
    const tooltip = d3.select("#my_dataviz")
        .append("div")
        .style("opacity", 0)
        .attr("class", "tooltip")
        .style("background-color", "white")
        .style("border", "solid")
        .style("border-width", "2px")
        .style("border-radius", "5px")
        .style("padding", "5px")

    // Three function that change the tooltip when user hover / move / leave a cell
    const mouseover = function(event,d) {
      tooltip
          .style("opacity", 1)
      d3.select(this)
          .style("stroke", "black")
          .style("opacity", 1)
    }
    const mousemove = function(event,d) {
      tooltip
          .html("The exact value of<br>this cell is: " + d.value)
          .style("left", (event.x)/2 + "px")
          .style("top", (event.y)/2 + "px")
    }
    const mouseleave = function(event,d) {
      tooltip
          .style("opacity", 0)
      d3.select(this)
          .style("stroke", "none")
          .style("opacity", 0.8)
    }

    // add the squares
    svg.selectAll()
        .data(data, function(d) {return d.group+':'+d.variable;})
        .join("rect")
        .attr("x", function(d) { return x(d.group) })
        .attr("y", function(d) { return y(d.variable) })
        .attr("rx", 4)
        .attr("ry", 4)
        .attr("width", x.bandwidth() )
        .attr("height", y.bandwidth() )
        .style("fill", function(d) {
          switch(d.variable) {
            case "Hubraum":
              return myColorH(d.value)
            case "Max. Geschwi.":
              return myColorM(d.value)
            case "Leistung":
              return myColorL(d.value)
            case "Preis":
              return myColorP(d.value)
          }
        } )
        .style("stroke-width", 4)
        .style("stroke", "none")
        .style("opacity", 0.8)
        .on("mouseover", mouseover)
        .on("mousemove", mousemove)
        .on("mouseleave", mouseleave)

// Add title to graph
    svg.append("text")
        .attr("x", 0)
        .attr("y", -50)
        .attr("text-anchor", "left")
        .style("font-size", "22px")
        .text("PKW Heatmap");

  }
}
</script>

<style>
/* Your CSS styling can go here */
</style>
