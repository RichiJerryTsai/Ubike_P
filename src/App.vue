<script setup>
import { ref, computed, onMounted } from 'vue'
import SearchPanel from './components/SearchPanel.vue'
import StationTable from './components/StationTable.vue'

const stations = ref([])
const dataLoadTime = ref('')
const isLoading = ref(true)

const query = ref('')
const selectedArea = ref('')
const showAll = ref(false)

const pad2 = (value) => String(value).padStart(2, '0')
//格式化日期時間，月份要特別注意 + 1
const formatDate = (date) =>
  `${date.getFullYear()}-${pad2(date.getMonth() + 1)}-${pad2(date.getDate())} ${pad2(date.getHours())}:${pad2(date.getMinutes())}:${pad2(date.getSeconds())}`

// 從官方 API 載入 YouBike 站點資料
const loadStations = async () => {
  isLoading.value = true
  try {
    const response = await fetch(
      'https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json'
    )
    if (!response.ok) throw new Error(`HTTP ${response.status}`)
    stations.value = await response.json()
    dataLoadTime.value = formatDate(new Date())
  } catch (error) {
    console.error('載入 YouBike 資料失敗：', error)
    stations.value = []
    dataLoadTime.value = '載入失敗'
  } finally {
    isLoading.value = false
  }
}
// 找到唯一的行政區名稱並且排序
const areas = computed(() => {
  return [...new Set(stations.value.map((s) => s.sarea))].sort((a, b) => a.length - b.length)
})

// 預設為依照可租借車輛數量排序
const filteredStations = computed(() => {
  const text = query.value.trim().toLowerCase()
  return stations.value.filter((station) => {
    const matchArea = selectedArea.value ? station.sarea === selectedArea.value : true
    const matchText = text
      ? station.sna.toLowerCase().includes(text) || station.ar.toLowerCase().includes(text)
      : true
    const matchAvailable = showAll.value ? station.available_rent_bikes > 0 : true
    return matchArea && matchText && matchAvailable
  })
  .sort((a, b) => b.available_rent_bikes - a.available_rent_bikes)
})

//點擊最新資料後重置搜尋條件並且重新載入資料
const handleRefresh = () => {
  query.value = ''
  selectedArea.value = ''
  showAll.value = false
  loadStations()
}
// 載入資料
onMounted(() => {
  loadStations()
})
</script>

<template>
  <main class="app-shell">
    <section class="hero" v-if="isLoading">
      <div>
        <p class="eyebrow">Ubike 站點搜尋</p>
        <h1>資料載入中...</h1>
        <p class="subtitle">請稍候，正在從官方 API 取得最新 Ubike 站點資料。</p>
      </div>
    </section>

    <template v-else>
      <section class="hero">
        <div>
          <p class="eyebrow">YouBike2.0 站點搜尋</p>
          <h1>開始查找台灣 YouBike2.0 站點</h1>
          <p class="subtitle">輸入站名、地址或區域，快速查看可用車輛與空位資訊。</p>
        </div>
        <!-- 搜尋條件面板 -->
        <SearchPanel
          v-model:query="query"
          v-model:selectedArea="selectedArea"
          v-model:showAll="showAll"
          :areas="areas"
          @refresh="handleRefresh"
        />
      </section>

      <section class="results">
        <StationTable
          :stations="filteredStations"
          :totalCount="filteredStations.length"
          :dataLoadTime="dataLoadTime"
        />
      </section>
    </template>
  </main>
</template>

<style scoped>
.app-shell {
  width: 100%;
  max-width: 100%;
  margin: 0;
  padding: 24px;
}

.hero {
  display: grid;
  gap: 24px;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--radius);
  box-shadow: var(--shadow);
  padding: 32px;
}

.eyebrow {
  margin: 0 0 12px;
  color: var(--primary);
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.1em;
  font-size: 0.85rem;
}

h1 {
  margin: 0;
  font-size: clamp(2.25rem, 2.5vw, 3rem);
  text-align: center;
}

.subtitle {
  margin: 16px 0 0;
  color: var(--muted);
  max-width: 720px;
}

.results {
  margin-top: 28px;
}
</style>
