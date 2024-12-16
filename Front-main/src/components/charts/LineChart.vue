<template>
    <div>
      <div id="weeklyTasksChart" style="width: 100%; height: 400px;"></div>
      <p v-if="!chartData || chartData.length === 0">Aucune donnée à afficher pour cette semaine.</p>
    </div>
  </template>
  
  <script>
  import * as echarts from "echarts";
  
  export default {
    name: "LineChart",
    props: {
      chartData: {
        type: Array,
        required: true,
      },
    },
    data() {
      return {
        chart: null, // Instance ECharts
      };
    },
    watch: {
      // Met à jour le graphique si les données changent
      chartData: {
        handler(newData) {
          if (this.chart) {
            this.updateChart(newData);
          }
        },
        deep: true,
      },
    },
    mounted() {
      this.createChart();
    },
    methods: {
      createChart() {
        const chartDom = document.getElementById("weeklyTasksChart");
        this.chart = echarts.init(chartDom);
  
        // Données initiales
        const options = this.generateChartOptions(this.chartData);
  
        // Appliquer les options au graphique
        this.chart.setOption(options);
      },
      updateChart(newData) {
        // Mettre à jour les options du graphique
        const options = this.generateChartOptions(newData);
        this.chart.setOption(options);
      },
      generateChartOptions(data) {
        // Labels fixes des jours de la semaine
        const days = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"];
  
        // Initialiser les données à zéro pour chaque jour
        const weekData = {
          Monday: 0,
          Tuesday: 0,
          Wednesday: 0,
          Thursday: 0,
          Friday: 0,
          Saturday: 0,
          Sunday: 0,
        };
  
        // Remplir les données avec les valeurs de la prop `chartData`
        data.forEach((entry) => {
          weekData[entry.day_of_week] = entry.completed_tasks;
        });
  
        // Retourner les options du graphique
        return {
          xAxis: {
            type: "category",
            data: days, // Labels des jours
          },
          yAxis: {
            type: "value",
          },
          series: [
            {
              data: Object.values(weekData), // Données correspondantes
              type: "line",
              color:"#5a189a",
              smooth: true, // Ligne lissée
            },
          ],
          tooltip: {
            trigger: "axis",
          },
          title: {
        
            left: "center",
          },
        };
      },
    },
  };
  </script>
  
 

  