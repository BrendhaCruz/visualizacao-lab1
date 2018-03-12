---

title: "nono"

date: 2018-03-010T23:38:58-02:00

draft: false
---

<style>
  .node {
      fill: #ccc;
      stroke: #fff;
      stroke-width: 2px;
  }

  .link {
      stroke: #999;
      stroke-opacity: 0.7;
  }


</style>

  <script>
    var
        width = 1000,
        height = 1000;

    //var path = d3.;

    var svg = d3.select("#chart")
        .append("svg")
        .attr('version', '1.1')
        .attr('viewBox', '0 0 '+width+' '+height)
        .attr('width', '100%');




    //var color = d3.scaleOrdinal(d3.schemeCategory20);

    var color = d3.scaleThreshold()
      .domain([30, 40, 50, 60, 70, 80, 95])
      .range(d3.schemeYlOrRd[7]);

    var simulation = d3.forceSimulation()
        .force("link", d3.forceLink().id(function(d)  { return d.id; }))
        .force("charge", d3.forceManyBody().strength(-45))
        .force("center", d3.forceCenter(width / 2, height / 2));

    d3.json("arctic.json", function(error, graph) {
      if (error) throw error;

      console.dir(graph.edges);
      console.dir(graph.nodes);

      var link = svg.append("g")
          .attr("class", "link")
        .selectAll("line")
          .data(graph.edges)
        .enter().append("line")
          .attr("stroke-width", 0.5);

      var node = svg.append("g")
          .attr("class", "nodes")
        .selectAll("circle")
          .data(graph.nodes)
        .enter().append("circle")
          .attr("r", function(d) { return d.size/10; })
          .attr("fill", function(d) { if(d.label === "Arctic Monkeys"){ return "#333"} return color(d.size); })
          .call(d3.drag()
              .on("start", dragstarted)
              .on("drag", dragged)
              .on("end", dragended));



      node.append("title")
          .text(function(d) { return d.label; });

      simulation
          .nodes(graph.nodes)
          .on("tick", ticked);

      simulation.force("link")
          .links(graph.edges);

      //add zoom capabilities
      var zoom_handler = d3.zoom()
          .on("zoom", zoom_actions);

      zoom_handler(svg);

      //specify what to do when zoom event listener is triggered
      function zoom_actions(){
      d3.selectAll("g").attr("transform", d3.event.transform);
      }

      function ticked() {
        link
            .attr("x1", function(d) { return d.source.x; })
            .attr("y1", function(d) { return d.source.y; })
            .attr("x2", function(d) { return d.target.x; })
            .attr("y2", function(d) { return d.target.y; });

        node
            .attr("cx", function(d) { return d.x; })
            .attr("cy", function(d) { return d.y; });
      }
    });

    function dragstarted(d) {
      if (!d3.event.active) simulation.alphaTarget(0.3).restart();
      d.fx = d.x;
      d.fy = d.y;
    }

    function dragged(d) {
      d.fx = d3.event.x;
      d.fy = d3.event.y;
    }

    function dragended(d) {
      if (!d3.event.active) simulation.alphaTarget(0);
      d.fx = null;
      d.fy = null;
    }

</script>
