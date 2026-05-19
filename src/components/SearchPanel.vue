<script setup>
const query = defineModel('query')
const selectedArea = defineModel('selectedArea')
const showAll = defineModel('showAll')

defineProps({
  areas: {
    type: Array,
    required: true
  }
})

const emit = defineEmits(['refresh'])
</script>

<template>
  <div class="search-panel">
    <label>
      <span>站名或地點查詢</span>
      <input type="search" v-model="query" placeholder="例如：科技大樓、復興南路、捷運 OO 站" />
    </label>

    <label>
      <span>所在行政區</span>
      <select v-model="selectedArea">
        <option value="">不拘</option>
        <option v-for="area in areas" :key="area" :value="area">
          {{ area }}
        </option>
      </select>
    </label>

    <div class="search-actions">
      <label class="checkbox-field">
        <input id="show-all" type="checkbox" v-model="showAll" />
        <span>只顯示有車輛的站點</span>
      </label>

      <button type="button" class="secondary" @click="emit('refresh')">
        <img src="/Pics/reload.svg" alt="" class="reload-btn-icon" />
        最新資料
      </button>
    </div>
  </div>
</template>

<style scoped>
.search-panel {
  display: grid;
  gap: 16px;
  grid-template-columns: 1fr 1fr auto;
  align-items: end;
}

.search-panel label {
  display: grid;
  gap: 8px;
  text-align: left;
  color: var(--text);
  margin: 0;
}

.search-panel .checkbox-field {
  display: inline-flex;
  align-items: center;
  gap: 8px;
}

.search-panel input,
.search-panel select {
  width: 100%;
  height: 50px;
  border: 1px solid var(--border);
  border-radius: 14px;
  padding: 0 16px;
  background: #ffffff;
  color: var(--text);
  font: inherit;
}

.search-panel input:focus,
.search-panel select:focus {
  outline: 2px solid rgba(37, 99, 235, 0.25);
}

.search-actions {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 12px;
}

.checkbox-field {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  font-size: 0.95rem;
  color: var(--text);
}

.checkbox-field input[type='checkbox'] {
  width: 18px;
  height: 18px;
  margin: 0;
}

.reload-btn-icon {
  width: 12px;
  height: 12px;
  margin-right: 4px;
  vertical-align: middle;
}

@media (max-width: 840px) {
  .search-panel {
    grid-template-columns: 1fr;
  }
}
</style>
