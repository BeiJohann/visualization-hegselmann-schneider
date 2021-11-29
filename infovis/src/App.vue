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
    // your code goes here
    // d3.select('#container').style("border", "5px solid gray" )

    //let data = new Array(6)
    //data[0] = [0,0,0,1,0,0]
    //data[1] = [0,0,2,0,0,4]
    //data[2] = [0,2,0,3,0,0]
    //data[3] = [1,0,3,0,1,0]
    //data[4] = [0,0,0,1,0,1]
    //data[5] = [0,4,0,0,1,0]

    let data = {
      "nodes": [
        {"id": 1, "name": "A", "geschlecht": "m", "beruf": "Informatiker"},
        {"id": 2, "name": "B", "geschlecht": "w", "beruf": "Informatiker"},
        {"id": 3, "name": "C", "geschlecht": "m", "beruf": "Student"},
        {"id": 4, "name": "D", "geschlecht": "d", "beruf": "Student"},
        {"id": 5, "name": "E", "geschlecht": "m", "beruf": "Autor"},
        {"id": 6, "name": "F", "geschlecht": "w", "beruf": "Autor"}],
      "links": [
        {"source": 3, "target": 2, "weight": 2},
        {"source": 3, "target": 4, "weight": 3},
        {"source": 4, "target": 1, "weight": 1},
        {"source": 4, "target": 5, "weight": 1},
        {"source": 5, "target": 6, "weight": 1},
        {"source": 6, "target": 2, "weight": 4}]}


    const margin = {top: 10, right: 30, bottom: 30, left: 40},
        width = 600 - margin.left - margin.right,
        height = 600 - margin.top - margin.bottom;

// append the svg object to the body of the page
    const svg = d3.select("#my_dataviz")
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform",
            `translate(${margin.left}, ${margin.top})`);


      // Initialize the links
      const link = svg
          .selectAll("line")
          .data(data.links)
          .join("line")
          .style("stroke", "#aaa")
          .attr("stroke-width", function(d) { return (d.weight * 3); })

      // Initialize the nodes
      const node = svg
          .selectAll("circle")
          .data(data.nodes)
          .join("circle")
          .attr("r", 20)
          .style("fill",function(d) {
            switch(d.beruf) {
              case "Informatiker":
                return "#804045"
              case "Student":
                return "#8fb369"
              case "Autor":
                return "#404080"
            }
          })
          .attr("text", function(d) { return (d.geschlecht); })

      // Let's list the force we wanna apply on the network
      const simulation = d3.forceSimulation(data.nodes)                 // Force algorithm is applied to data.nodes
          .force("link", d3.forceLink()                               // This force provides links between nodes
              .id(function(d) { return d.id; })                     // This provide  the id of a node
              .links(data.links)                                    // and this the list of links
          )
          .force("charge", d3.forceManyBody().strength(-400))         // This adds repulsion between nodes. Play with the -400 for the repulsion strength
          .force("center", d3.forceCenter(width / 2, height / 2))     // This force attracts nodes to the center of the svg area
          .on("end", ticked);

      // This function is run at each iteration of the force algorithm, updating the nodes position.
      function ticked() {
        link
            .attr("x1", function(d) { return d.source.x; })
            .attr("y1", function(d) { return d.source.y; })
            .attr("x2", function(d) { return d.target.x; })
            .attr("y2", function(d) { return d.target.y; });

        node
            .attr("cx", function (d) { return d.x+6; })
            .attr("cy", function(d) { return d.y-6; });
      }

    // Handmade legend
    svg.append("circle").attr("cx",400).attr("cy",40).attr("r", 6).style("fill", "#804045")
    svg.append("circle").attr("cx",400).attr("cy",70).attr("r", 6).style("fill", "#8fb369")
    svg.append("circle").attr("cx",400).attr("cy",100).attr("r", 6).style("fill", "#404080")
    svg.append("text").attr("x", 420).attr("y", 40).text("Informatiker").style("font-size", "15px").attr("alignment-baseline","middle")
    svg.append("text").attr("x", 420).attr("y", 70).text("Student").style("font-size", "15px").attr("alignment-baseline","middle")
    svg.append("text").attr("x", 420).attr("y", 100).text("Autor").style("font-size", "15px").attr("alignment-baseline","middle")

  }
}
</script>

<style>
/* Your CSS styling can go here */
</style>
