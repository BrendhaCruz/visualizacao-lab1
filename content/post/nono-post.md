---

title: "nono"

date: 2018-03-010T23:38:58-02:00

draft: false
---
<p><link href="https://fonts.googleapis.com/css?family=Open+Sans|Roboto+Slab" rel="stylesheet">
<link href="css/bootstrap.min.css" rel="stylesheet">
<link href="css/main.css" rel="stylesheet">
<link href="css/fonts.css" rel="stylesheet"></p>

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


<body>

  <div class="container">

    <div class="header">
      <div class="row">
        <div class="col-xs-10">
          <h2>Force layout</h2>
        </div>
      </div>
    </div>
    <ol class="breadcrumb">
      <li><a href="networks1.html">1</a></li>
      <li><a href="networks2.html">2</a></li>
      <li><a href="networks3.html">3</a></li>
      <li><a href="networks4.html">4</a></li>
    </ol>
    <header>
      <h1>Minha preferida</h1>
    </header>

    <div id="chart"></div>

    <div class="section">

      <div class="row">
        <div class="col-md-8">
          <h3>O que</h3>
          <p>Para esse exemplo, usamos dados de quais artistas são semelhantes a quais da API do Spotify. A partir de um artista inicial, usamos um programa que pega artistas semelhantes a ele, e semelhantes a estes, até atingir um limite. Faça hover para entender quais.</p>
          <h3>Topologia</h3>
          <p>Você consegue perceber uma estrutura em comunidades no grafo? Se sim, dá para interpretar as comunidades? Os dados do spotify refletem seu conhecimento sobre esses artistas?</p>
          <h3>Melhore</h3>
          <p>Sua missão aqui é personalizar e tornar mais legível a visualização. Para isso faça o seguinte:</p>
          <ol>
            <li>Mude os dados. Nessa visualização, estou usando os dados do backend desse <a href="http://labs.polsys.net/playground/spotify/">sistema aqui</a>. A forma como chego no json é (a) fazendo uma busca com o endpoint getartists:  http://labs.polsys.net/playground/spotify/getartists.php?query=Mc%20Loma , (b) vendo o id do artista que me interessa, e depois usando esse outro endpoint e salvando o json: http://labs.polsys.net/playground/spotify/getnet.php?id=6tdM5Njlln7nWkonCppW0u . Tudo no navegador  </li>
            <li>Mude as forças. Como no exemplo anterior</li>
            <li>Você pode também filtrar fora nós e links. O tamanho do nó é proporcional à popularidade do artista. Javascript tem uma função filter() nos arrays</li>
            <li>Brinque à vontade com o css. As vezes tirar a ênfase dos links ajuda, as vezes deixar nós transparentes. Quer mudar o tamanho deles também? Cor? Vai lá.</li>
          </ol>
          <p>Aqui o principal é que você experimente com como tornar um grafo mais legível e comunicar sua análise.</p>
        </div>
      </div>
    </div>
  </div>

  <footer></footer>

  <!-- scripts -->
  <script src="js/d3.v4.min.js"></script>
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

</body>
