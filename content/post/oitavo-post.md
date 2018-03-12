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


  <style>

  .axis--x path {
    display: none;
  }
  .line {
    fill: none;
    stroke: steelblue;
    stroke-width: 1.5px;
  }
     </style>

     <svg width="960" height="300"></svg>
     <script src="https://d3js.org/d3.v4.min.js"></script>
     <script>
     //var path = 'https://raw.githubusercontent.com/gabrielmla/portfolio/master/dados/lab2-parte4/';
         var dados = 'bobs.csv';

     var svg = d3.select("svg"),
         margin = {top: 20, right: 80, bottom: 30, left: 50},
         width = svg.attr("width") - margin.left - margin.right,
         height = svg.attr("height") - margin.top - margin.bottom,
         g = svg.append("g").attr("transform", "translate(" + margin.left + "," + margin.top + ")");
     var parseTime = d3.timeParse("%H:%M");
     var x = d3.scaleTime().range([0, width]),
         y = d3.scaleLinear().range([height, 0]),
         z = d3.scaleOrdinal(d3.schemeCategory10);
     var line = d3.line()
         .curve(d3.curveBasis)
         .x(function(d) { return x(d.hora); })
         .y(function(d) { return y(d.quantidade); });
     d3.csv(dados, type, function(error, data) {
       if (error) throw error;
       var categorias = data.columns.slice(1).map(function(id) {
         return {
           id: id,
           values: data.map(function(d) {
             return {hora: d.horario_inicial, quantidade: d[id]};
           })
         };
       });
       x.domain(d3.extent(data, function(d) { return d.horario_inicial; }));
       y.domain([
         d3.min(categorias, function(c) { return d3.min(c.values, function(d) { return d.quantidade; }); }),
         d3.max(categorias, function(c) { return d3.max(c.values, function(d) { return d.quantidade; }); })
       ]);
       z.domain(categorias.map(function(c) { return c.id; }));


       g.append("g")
           .attr("class", "axis axis--x")
           .attr("transform", "translate(0," + height + ")")
           .call(d3.axisBottom(x));
       g.append("g")
           .attr("class", "axis axis--y")
           .call(d3.axisLeft(y))
         .append("text")
           .attr("transform", "rotate(-90)")
           .attr("y", 6)
           .attr("dy", "0.71em")
           .attr("fill", "#000");

       var categoria = g.selectAll(".categoria")
         //.data(dados.filter((d) => d.local == "jackson"))
         .data(categorias)
         .enter().append("g")
           .attr("class", "categoria");
       categoria.append("path")
           .attr("class", "line")
           .attr("d", function(d) { return line(d.values); })
           .style("stroke", function(d) { return z(d.id); });
       categoria.append("text")
           .datum(function(d) { return {id: d.id, value: d.values[d.values.length - 1]}; })
           .attr("transform", function(d) { return "translate(" + x(d.value.hora) + "," + y(d.value.quantidade) + ")"; })
           .attr("x", 3)
           .attr("dy", "0.35em")
           .style("font", "10px sans-serif")
           .text(function(d) { return d.id; });


    categoria.append("text")
      .attr("transform", "translate(-30," + (height + margin.top)/2 + ") rotate(-90)")
     .style("font", "12px sans-serif")
     .text("Quantidade Ciclistas");

   categoria.append("text")
       //.attr("transform", "translate(" + width + ")")
        .attr("transform", "translate(" + width/2 +"," + (height+margin.bottom) + ")")
       .style("font", "12px sans-serif")
       .text("Horário");

     });

     function type(d, _, columns) {
         d.horario_inicial = parseTime(d.horario_inicial);
       for (var i = 1, n = columns.length, c; i < n; ++i) d[c = columns[i]] = +d[c];
       return d;
     }


  </script>
