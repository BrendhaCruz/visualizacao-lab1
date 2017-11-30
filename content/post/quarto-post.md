---
title: "Percentil dos meses chuvosos e não chuvosos"
date: 2017-11-29T22:40:35-03:00
draft: false
---

<script src="https://d3js.org/d3.v4.min.js"></script>

<body>
  <div class="container">
    <div class="row">
      <h2>Percentil dos meses chuvosos e não chuvosos</h2>
    </div>
    <div class="row mychart" id="chart">
    </div>
  </div>


  <script type="text/javascript">
    "use strict"

  function desenhaBarras(dados) {
          console.log(dados)
          var alturaSVG = 400, larguraSVG = 900;
          var	margin = {top: 0, right: 20, bottom:30, left: 45},
              larguraVis = larguraSVG - margin.left - margin.right,
              alturaVis = alturaSVG - margin.top - margin.bottom;


          var grafico = d3.select('#chart')
            .append('svg')
              .attr('width', larguraVis + margin.left + margin.right)
              .attr('height', alturaVis + margin.top + margin.bottom)
            .append('g')
              .attr('transform', 'translate(' +  margin.left + ',' + margin.top + ')');


        console.log(dados)

        var max90 = d3.max(dados, function(d) {   
            return d['noventa-percentil']+1;   
        });

        var min90 = d3.min(dados, function(d) {    
            return d['noventa-percentil']-1;  
        });

        var x = d3.scaleLinear()
            x.domain([min90, max90])
            x.range([0,larguraVis])

        var max10 = d3.max(dados, function(d) {    
            return d['dez_percentil'] +1;  
        });

        var min10 = d3.min(dados, function(d) {    
            return d['dez_percentil']-1;  
        });

        var y = d3.scaleLinear()
            y.domain([min10,max10])
            y.range([alturaVis,0]);  

        var color = function(dado){
          if (dado.mes > 0 & dado.mes < 5  ) {
            return 'blue'
          }
          else{
            return 'orange'
          }

        }

        function mes(num) {
          var str = '';
          if(num === 1){
            return str = 'Janeiro'
          }
          if(num === 2){
            return str = 'Fevereiro'
          }
          if(num === 3){
            return str = 'Março'
          }if(num === 4){
            return str = 'Abril'
          }if(num === 5){
            return str = 'Maio'
          }if(num === 6){
            return str = 'Junho'
          }if(num === 7){
            return str = 'Julho'
          }if(num === 8){
            return str = 'Agosto'
          }if(num === 9){
            return str = 'Setembro'
          }if(num === 10){
            return str = 'Outubro'
          }if(num === 11){
            return str = 'Novembro'
          }if(num === 12){
            return str = 'Dezembro'
          }


          return str}

        grafico.selectAll('g')
                .data(dados)
                .enter()
                  .append('circle')
                    .attr('cx', d => x(d['noventa-percentil']))
                    .attr('cy', d => y(d['dez_percentil']))
                    .attr('r', 10)
                    .attr('height', (d) => alturaVis - y(d['dez_percentil']))
                    .attr('fill', (d) => color(d))

        grafico.selectAll('g')
  			.data(dados)
  			.enter()
  			.append('text')
  			.attr("x", d => x(d['noventa-percentil']))
  			.attr("y", d => y(d['dez_percentil']))
  			.text(d => mes(d.mes));

        grafico.append("g")
                .attr("class", "x axis")
                .attr("transform", "translate(0," + alturaVis + ")")
                .call(d3.axisBottom(x));

        grafico.append('g')
                .attr('transform', 'translate(0,0)')
                .call(d3.axisLeft(y))  

        grafico.append("text")
          .attr("transform", "translate(-40," + (alturaVis + margin.top)/2 + ") rotate(-90)")
          .text("10 percentil");

        grafico.append("text")
            .attr("transform", "translate(" + larguraVis/2 +"," + (alturaVis+margin.bottom) + ")")
            .text("90 percentil");
      }
    d3.json('boqueirao-por-mes.json', function(dados) {
      console.log("provavelmente acontece depois")
      desenhaBarras(dados);
    });
    console.log("provavelmente acontece primeiro")

  </script>
</body>
