<script setup>
defineProps({
  stations: {
    type: Array,
    required: true,
  },
  totalCount: {
    type: Number,
    required: true,
  },
  dataLoadTime: {
    type: String,
    required: true,
  },
});

const formatName = (sna) => sna.replace("YouBike2.0_", "");
const formatCoord = (lat, lng) => `${lat.toFixed(6)},  ${lng.toFixed(6)}`;
const openMap = (lat, lng) => {
  window.open(`https://www.google.com/maps?q=${lat},${lng}`, "_blank");
};
</script>

<template>
  <div class="results-header">
    <h2>搜尋結果</h2>
    <p>{{ totalCount }} 個站點符合</p>
  </div>

  <table class="results-table">
    <thead>
      <tr>
        <th>項次</th>
        <th>場站區域</th>
        <th>站名</th>
        <th>地點</th>
        <th>坐標位置</th>
        <th>目前車輛數</th>
        <th>目前空位數</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(station, index) in stations.slice(0, 50)" :key="station.sno" :title="`資料時間：${dataLoadTime}`">
        <td>{{ index + 1 }}</td>
        <td>{{ station.sarea }}</td>
        <td>{{ formatName(station.sna) }}</td>
        <td>{{ station.ar }}</td>
        <td class="coord-cell">
          <button class="location-btn" @click="openMap(station.latitude, station.longitude)" title="在 Google Maps 開啟">
            <img src="/Pics/location.jpg" alt="定位" />
          </button>
          {{ formatCoord(station.latitude, station.longitude) }}
        </td>
        <td>{{ station.available_rent_bikes }}</td>
        <td>{{ station.available_return_bikes }}</td>
      </tr>
    </tbody>
  </table>

  <div class="table-footer">
    <div>資料筆數：{{ totalCount }}</div>
    <div>資料更新時間：{{ dataLoadTime }}</div>
  </div>
</template>

<style scoped>
.results-header {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  gap: 12px;
  align-items: baseline;
  margin-bottom: 16px;
  
}

.results-header h2 {
  margin: 0;
}

.results-table {
  width: 100%;
  border-collapse: collapse;
  background: #ffffff;
  box-shadow: 0 12px 30px rgba(15, 23, 42, 0.06);
  border-radius: 24px 24px 0 0;
  border-left: 1px solid var(--border);
  border-right: 1px solid var(--border);
}

.results-table th,
.results-table td {
  padding: 14px 16px;
  border-bottom: 1px solid #e5e7eb;
  text-align: left;
}

.results-table th {
  background: #239bb0;
  color: #ffffff;
  font-weight: 700;
}

.results-table tbody tr:nth-child(odd) {
  background: #f2f2f2;
}

.results-table tbody tr:last-child td {
  border-bottom: none;
}

.coord-cell {
  display: flex;
  align-items: center;
  gap: 8px;
}

.location-btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 28px;
  height: 28px;
  padding: 0;
  border: none;
  background: none;
  flex-shrink: 0;
}

.location-btn img {
  width: 20px;
  height: 20px;
  object-fit: contain;
}

.location-btn:hover {
  transform: scale(1.2);
  transition: transform 0.15s ease;
}

.table-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  min-height: 50px;
  padding: 0 16px;
  background: #ffffff;
  border: 1px solid var(--border);
  border-top: 1px solid var(--border);
  color: var(--muted);
}

@media (max-width: 840px) {

  .results-header,
  .table-footer {
    flex-direction: column;
    align-items: flex-start;
  }

  .table-footer {
    gap: 8px;
  }
}
</style>
