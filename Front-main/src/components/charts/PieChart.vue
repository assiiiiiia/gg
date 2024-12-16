<template>
   
  
  <v-chart :option="chartOptions" 
           style="width: 500px; height: 400px;" 
           :class="{'dashboard-pie-chart': isInDashboard}" />

</template>

<script>
import { defineComponent, watch, ref } from 'vue';
import { use } from 'echarts/core';
import { PieChart } from 'echarts/charts';
import { TitleComponent, TooltipComponent, LegendComponent } from 'echarts/components';
import { CanvasRenderer } from 'echarts/renderers';
import VChart from 'vue-echarts';

// Register necessary ECharts components
use([PieChart, TitleComponent, TooltipComponent, LegendComponent, CanvasRenderer]);

export default defineComponent({
  name: 'PieChart',
  components: { VChart },
  props: {
    statistics: {
      type: Array,
      required: true,
    },
  },
  setup(props) {
    const chartOptions = ref({
      tooltip: {
        trigger: 'item',
        formatter: '{b}: {c} ({d}%)', // Custom tooltip format
      },
      legend: {
        top: '5%',
        left: 'center',
        orient:'horizontal',
        textStyle: {
          color: '#333', // Customize legend text color
          fontSize: 13,
        },
      },
      color: ['#5a189a', '#e0aaff', '#9d4edd', '#10002b', '#73C0DE'], // Custom colors for pie slices
      series: [
        {
          name: 'Task Statistics',
          type: 'pie',
          radius: '50%',
          data: [], // Dynamically populated
          label: {

            color: '#333', // Customize label text color
            fontSize: 12,
          },
          emphasis: {
            itemStyle: {
              shadowBlur: 10,
              shadowOffsetX: 0,
              shadowColor: 'rgba(0, 0, 0, 0.5)',
            },
          },
        },
      ],
    });

    // Watch for statistics prop updates
    watch(
      () => props.statistics,
      (newStatistics) => {
        // Update the chart data with modified status names
        chartOptions.value.series[0].data = newStatistics.map((stat) => {
          let modifiedStatus = stat.status;
          if (modifiedStatus === 'termine') {
            modifiedStatus = 'Terminé';
          } else if (modifiedStatus === 'pas commence') {
            modifiedStatus = 'Pas commencé';
          } else if (modifiedStatus === 'annule') {
            modifiedStatus = 'Annulé';
          }

          return {
            value: stat.count,
            name: modifiedStatus, // Use modified status name
          };
        });
      },
      { immediate: true }
    );

    return { chartOptions };
  },
});
</script>

<style scoped>
/* Default styling */
.dashboard-pie-chart {
  border: 1px solid #ddd;
 
  border-radius: 12px;
  padding: 2.3rem;
  margin-bottom: 30px;
  background:#f7eaff;
  width:100%;
  max-height: 400px;
  box-shadow: 0 8px 15px rgba(0, 0, 0, 0.1);             /* Padding around the pie chart */
}
</style>
