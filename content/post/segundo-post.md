---

title: "Menor Volume de Água já Registrado"

date: 2017-11-14T23:38:58-02:00

draft: false
---

<div id="vis" width=300></div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/vega/3.0.7/vega.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/vega-lite/2.0.1/vega-lite.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/vega-embed/3.0.0-rc7/vega-embed.js"></script>
<script>
    const spec = {
      "$schema": "https://vega.github.io/schema/vega-lite/v2.json",
        "layer": [{
        "data": {
            "url": "https://api.insa.gov.br/reservatorios/12172/monitoramento",
            "format": {
            "type": "json",
            "property": "volumes",
            "parse": {
                "DataInformacao": "utc:'%d/%m/%Y'"
                    }
                }
            },

        "width": 800,
        "height": 150,

        "mark":"bar",
        "encoding": {
        "x": {"field": "DataInformacao", "type": "temporal", "timeUnit": "year",
                "axis":{
                "title": "Volume ao longo dos anos"
                }
        },
        "y": {
            "field": "Volume", "type": "quantitative",
              "axis":{
                "title": "Volume"
              },
              "aggregate":"mean"
            }
      }
       },



    {
      "data": {"values": [{"y" : 22}]},
      "mark": "rule",
      "encoding": {
        "y": {"field": "y","type": "quantitative"},
        "color": {"value": "orange"},
        "size": {"value": 2}
      }

    }
        ]
      }

;
  	vegaEmbed('#vis', spec).catch(console.warn);
</script>
