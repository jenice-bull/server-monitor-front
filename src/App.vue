<template>
  <div class="container">
    <h1 class="title">服务器监控面板</h1>

    <div class="toolbar">
      <button @click="fetchData">刷新</button>
    </div>

    <div class="grid" v-if="status">
      <!-- CPU -->
      <div
        class="card"
        :class="getPercentClass(status.cpu_percent)"
      >
        <p class="label">CPU 使用率</p>
        <p class="value">
          {{ status.cpu_percent }}%
        </p>
      </div>

      <!-- 内存 -->
      <div
        class="card"
        :class="getPercentClass(status.memory.percent)"
      >
        <p class="label">内存使用率</p>
        <p class="value">
          {{ status.memory.percent }}%
        </p>
      </div>

      <!-- 磁盘 -->
      <div
        class="card"
        :class="getPercentClass(status.disk.percent)"
      >
        <p class="label">磁盘使用率</p>
        <p class="value">
          {{ status.disk.percent }}%
        </p>
      </div>

      <!-- 进程数 -->
      <div class="card normal">
        <p class="label">进程数</p>
        <p class="value">
          {{ status.process_count }}
        </p>
      </div>

      <!-- 平均负载 -->
      <div class="card wide normal">
        <p class="label">平均负载 (1 / 5 / 15 分钟)</p>
        <p class="value small">
          {{ status.load_avg.join(' , ') }}
        </p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'

const status = ref(null)

/**
 * 获取服务器状态
 */
const fetchData = async () => {
  try {
    const res = await axios.get('http://127.0.0.1:8000/api/status/')
    status.value = res.data
  } catch (err) {
    console.error('获取服务器状态失败', err)
  }
}

/**
 * 根据百分比返回状态 class
 */
const getPercentClass = (value) => {
  if (value >= 80) return 'danger'
  if (value >= 60) return 'warning'
  return 'normal'
}

// 页面加载时获取一次数据
fetchData()
</script>

<style scoped>
/* 页面基础 */
.container {
  max-width: 1000px;
  margin: 40px auto;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI",
    Roboto, "Helvetica Neue", Arial;
}

/* 标题 */
.title {
  text-align: center;
  font-size: 42px;
  margin-bottom: 30px;
  color: #1f2937;
}

/* 工具栏 */
.toolbar {
  text-align: center;
  margin-bottom: 30px;
}

/* 按钮 */
button {
  background: #3b82f6;
  border: none;
  color: #fff;
  padding: 10px 26px;
  font-size: 16px;
  border-radius: 6px;
  cursor: pointer;
}

button:hover {
  background: #2563eb;
}

/* 卡片布局 */
.grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 20px;
}

/* 卡片基础样式 */
.card {
  background: #fff;
  border-radius: 12px;
  padding: 24px;
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.08);
}

/* 占两列 */
.card.wide {
  grid-column: span 2;
}

/* 描述文字 */
.label {
  font-size: 14px;
  color: #6b7280;
  margin-bottom: 8px;
}

/* 数值 */
.value {
  font-size: 36px;
  font-weight: bold;
}

.value.small {
  font-size: 24px;
}

/* ========= 状态颜色 ========= */

/* 正常 */
.card.normal {
  border-left: 6px solid #16a34a;
}

.card.normal .value {
  color: #16a34a;
}

/* 警告 */
.card.warning {
  border-left: 6px solid #f59e0b;
}

.card.warning .value {
  color: #f59e0b;
}

/* 危险 */
.card.danger {
  border-left: 6px solid #dc2626;
}

.card.danger .value {
  color: #dc2626;
}
</style>
