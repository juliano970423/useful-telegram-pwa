<template>
  <div class="debug-container">
    <h3>計時器競態條件測試</h3>
    <div class="debug-info">
      <p>當前時間: {{ currentTime }}ms</p>
      <p>限時: {{ timeLimit }}ms</p>
      <p>剩餘時間: {{ remainingTime }}ms</p>
      <p>答題鎖定: {{ isAnswering }}</p>
      <p>測試活躍: {{ isTestActive }}</p>
    </div>
    
    <div class="test-controls">
      <mdui-button @click="startDebugTest" :disabled="isTestActive">
        開始測試
      </mdui-button>
      <mdui-button @click="simulateLateClick" :disabled="!isTestActive || isAnswering">
        模擬接近截止時間點擊
      </mdui-button>
      <mdui-button @click="resetDebug" :disabled="isTestActive">
        重置
      </mdui-button>
    </div>
    
    <div class="test-area" v-if="isTestActive">
      <div class="timer-display">
        <div class="timer-bar">
          <div class="timer-progress" :style="{ width: timerProgress + '%' }"></div>
        </div>
        <p>{{ Math.round(remainingTime) }}ms 剩餘</p>
      </div>
      
      <div class="answer-simulation">
        <mdui-button 
          @click="clickAnswer" 
          :disabled="isAnswering || currentTime >= timeLimit"
          variant="filled"
        >
          點擊答案 ({{ clickCount }})
        </mdui-button>
        
        <div class="result-log">
          <h4>答題記錄:</h4>
          <div v-for="(log, index) in resultLogs" :key="index" class="log-item">
            {{ log }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onUnmounted } from 'vue'

const timeLimit = ref(3000) // 3秒
const currentTime = ref(0)
const isTestActive = ref(false)
const isAnswering = ref(false)
const clickCount = ref(0)
const resultLogs = ref<string[]>([])
const timerInterval = ref<number | null>(null)

const timerProgress = computed(() => {
  return Math.max(0, 100 - (currentTime.value / timeLimit.value) * 100)
})

const remainingTime = computed(() => {
  return Math.max(0, timeLimit.value - currentTime.value)
})

function startDebugTest() {
  isTestActive.value = true
  isAnswering.value = false
  currentTime.value = 0
  clickCount.value = 0
  resultLogs.value = []
  
  resultLogs.value.push('測試開始')
  
  timerInterval.value = setInterval(() => {
    currentTime.value += 50
    
    if (currentTime.value >= timeLimit.value && !isAnswering.value) {
      handleTimeout()
    }
  }, 50)
}

function simulateLateClick() {
  // 模擬在接近截止時間時點擊
  currentTime.value = timeLimit.value - 100 // 剩餘100ms
  resultLogs.value.push(`模擬晚期點擊，剩餘時間: ${remainingTime.value}ms`)
}

function clickAnswer() {
  if (isAnswering.value || currentTime.value >= timeLimit.value) return
  
  isAnswering.value = true
  clickCount.value++
  resultLogs.value.push(`第${clickCount.value}次點擊 - 時間: ${currentTime.value}ms`)
  
  // 模擬答題處理時間
  setTimeout(() => {
    resultLogs.value.push(`答題處理完成`)
    nextDebugQuestion()
  }, 200)
}

function handleTimeout() {
  if (timerInterval.value) {
    clearInterval(timerInterval.value)
  }
  resultLogs.value.push(`超時處理 - 時間: ${currentTime.value}ms`)
  
  // 模擬超時處理
  setTimeout(() => {
    resultLogs.value.push(`超時處理完成`)
    nextDebugQuestion()
  }, 200)
}

function nextDebugQuestion() {
  isAnswering.value = false
  currentTime.value = 0
  resultLogs.value.push('準備下一題...')
  
  // 簡單重置計時器
  if (timerInterval.value) {
    clearInterval(timerInterval.value)
  }
  
  if (isTestActive.value) {
    timerInterval.value = setInterval(() => {
      currentTime.value += 50
      
      if (currentTime.value >= timeLimit.value && !isAnswering.value) {
        handleTimeout()
      }
    }, 50)
  }
}

function resetDebug() {
  isTestActive.value = false
  isAnswering.value = false
  currentTime.value = 0
  clickCount.value = 0
  resultLogs.value = []
  
  if (timerInterval.value) {
    clearInterval(timerInterval.value)
    timerInterval.value = null
  }
}

onUnmounted(() => {
  if (timerInterval.value) {
    clearInterval(timerInterval.value)
  }
})
</script>

<style scoped>
.debug-container {
  max-width: 600px;
  margin: 2rem auto;
  padding: 2rem;
  background: var(--md3-surface);
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.debug-info {
  background: var(--md3-surface-variant);
  padding: 1rem;
  border-radius: 8px;
  margin-bottom: 1rem;
}

.debug-info p {
  margin: 0.5rem 0;
  font-family: monospace;
}

.test-controls {
  display: flex;
  gap: 1rem;
  margin-bottom: 2rem;
  flex-wrap: wrap;
}

.test-area {
  text-align: center;
}

.timer-display {
  margin-bottom: 2rem;
}

.timer-bar {
  width: 100%;
  height: 20px;
  background: var(--md3-surface-variant);
  border-radius: 10px;
  overflow: hidden;
  margin-bottom: 1rem;
}

.timer-progress {
  height: 100%;
  background: var(--md3-primary);
  transition: width 0.05s linear;
  border-radius: 10px;
}

.answer-simulation {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

.result-log {
  text-align: left;
  background: var(--md3-surface-variant);
  padding: 1rem;
  border-radius: 8px;
  width: 100%;
  max-height: 200px;
  overflow-y: auto;
}

.result-log h4 {
  margin: 0 0 0.5rem 0;
}

.log-item {
  font-family: monospace;
  font-size: 0.9rem;
  margin: 0.25rem 0;
  padding: 0.25rem;
  background: var(--md3-surface);
  border-radius: 4px;
}
</style>