---

title: "Ciclistas no Açude Velho(não iterativo)"

date: 2018-03-010T23:38:58-02:00

draft: false
---


  <style>
      .bar {
      fill: #98abc5;
      }
  </style>



<svg width="960" height="500" id="graph"></svg>
<script src="https://d3js.org/d3.v4.min.js"></script>
  <script type="text/javascript">
    "use strict"


    // Set the margins
var margin = {top: 60, right: 100, bottom: 20, left: 80},
  width = 850 - margin.left - margin.right,
  height = 370 - margin.top - margin.bottom;

// Set the ranges
var x = d3.scaleBand().rangeRound([0, width]).padding(0.1)
var y = d3.scaleLinear().range([height, 0]);


// Create the svg canvas in the "graph" div
var svg = d3.select("#graph")
        .append("svg")
        .style("width", width + margin.left + margin.right + "px")
        .style("height", height + margin.top + margin.bottom + "px")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform","translate(" + margin.left + "," + margin.top + ")")
        .attr("class", "svg");

// Import the CSV data

d3.csv('https://raw.githubusercontent.com/luizaugustomm/pessoas-no-acude/master/dados/processados/dados.csv', function(error, data) {
  if (error) throw error;

   // Format the data
  data.forEach(function(d) {
      //d. = parseMonth(d.Month);
      d.total_ciclistas = +d.total_ciclistas;
      d.local = d.local;
  });

	var nest = d3.nest()
	  .key(function(d){
	    return d.local;
	  })
	  .sortKeys(d3.ascending)
	  .rollup(function(leaves){
	 		return d3.sum(leaves, function(d) {return (d.total_ciclistas)});
		})
	  .entries(data)

console.log(nest)
  // Scale the range of the data
  x.domain(nest.map(function(d) { return d.key; }));
  y.domain([0, d3.max(nest, function(d) { return d.value; })]);

  // Set up the x axis
  var xaxis = svg.append("g")
       .attr("transform", "translate(0," + height + ")")
       .attr("class", "x axis")
       .call(d3.axisBottom(x)
          //.ticks(d3.timeMonth)
          .tickSize(0, 0)
          //.tickFormat(d3.timeFormat("%B"))
          .tickSizeInner(0)
          .tickPadding(10));

  // Add the Y Axis
   var yaxis = svg.append("g")
       .attr("class", "y axis")
       .call(d3.axisLeft(y)
          .ticks(5)
          .tickSizeInner(0)
          .tickPadding(6)
          .tickSize(0, 0));

 // yaxis.select(".domain").style("display","none")

  // Add a label to the y axis
  svg.append("text")
        .attr("transform", "rotate(-90)")
        .attr("y", 0 - 60)
        .attr("x", 0 - (height / 2))
        .attr("dy", "1em")
        .style("text-anchor", "middle")
        .text("TOTAL DE CICLISTAS")
        .attr("class", "y axis label");

  // Draw the bars
  svg.selectAll(".rect")
      .data(nest)
      .enter()
      .append("rect")
      	  .attr("class", "bar")
	      .attr("x", function(d) { return x(d.key); })
	      .attr("y", function(d) { return y(d.value); })
	      .attr("width", x.bandwidth())
	      .attr("height", function(d) { return height - y(d.value); });

})

  </script>
