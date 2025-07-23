<template>
  <div style="max-width: 900px; margin: 40px auto;">
    <a-card title="工单列表">
      <a-table :columns="columns" :data-source="orders" row-key="id" bordered>
        <template #bodyCell="{ column, record }">
          <template v-if="column.key === 'action'">
            <a-button v-if="role === 'admin'" danger size="small" @click="onDelete(record.id)">删除</a-button>
          </template>
        </template>
      </a-table>
    </a-card>

    <a-card title="Project Hours Distribution" style="margin-top: 32px;">
      <div ref="chartRef" style="height: 360px; width: 100%;"></div>
    </a-card>
  </div>
</template>

<script setup>
import { ref, watch, onMounted, nextTick } from 'vue';
import * as echarts from 'echarts';

const role = localStorage.getItem('role') || 'admin';

const orders = ref([
  { id: '001', project: 'Road Project A', overtime: 'true', hours: 3.5, createdAt: '2024-04-10 10:30' },
  { id: '002', project: 'Bridge Maintenance B', overtime: 'false', hours: 2.0, createdAt: '2024-04-09 13:00' },
  { id: '003', project: 'Pipeline Fix C', overtime: 'true', hours: 4.5, createdAt: '2024-04-08 08:00' },
  { id: '004', project: 'Bridge Maintenance B', overtime: 'true', hours: 3.0, createdAt: '2024-04-07 16:45' },
  { id: '005', project: 'Tunnel Cleaning D', overtime: 'false', hours: 8.1, createdAt: '2024-04-03 11:43' }  
]);

const columns = [
  { title: 'ID', dataIndex: 'id', key: 'id' },
  { title: 'Project', dataIndex: 'project', key: 'project' },
  { title: 'Overtime', dataIndex: 'overtime', key: 'overtime' },
  { title: 'Hours', dataIndex: 'hours', key: 'hours' },
  { title: 'Created At', dataIndex: 'createdAt', key: 'createdAt' },
  { title: '操作', key: 'action' }
];

function onDelete(id) {
  orders.value = orders.value.filter(item => item.id !== id);
}

const chartRef = ref();
let chartInstance = null;

function getChartData() {
  // 按项目分组累计工时
  const stat = {};
  orders.value.forEach(item => {
    stat[item.project] = (stat[item.project] || 0) + Number(item.hours);
  });
  return {
    projects: Object.keys(stat),
    hours: Object.values(stat)
  };
}

function renderChart() {
  if (!chartInstance) {
    chartInstance = echarts.init(chartRef.value);
  }
  const { projects, hours } = getChartData();
  chartInstance.setOption({
    title: { text: '' },
    tooltip: {},
    xAxis: { type: 'category', data: projects },
    yAxis: { type: 'value', name: 'Hours' },
    series: [
      {
        type: 'bar',
        data: hours,
        itemStyle: { color: '#1890ff' },
        barWidth: 40
      }
    ]
  });
}

onMounted(() => {
  nextTick(renderChart);
});

watch(orders, () => {
  nextTick(renderChart);
}, { deep: true });
</script>

<style scoped>
</style>
