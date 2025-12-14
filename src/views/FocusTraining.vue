<template>
  <div class="focus-training-container">
    <NavBar />
    
    <main class="main-content">
      <div class="page-header">
        <h1 class="page-title">專注力訓練</h1>
        <p class="page-description">
          透過科學化的認知訓練遊戲，提升你的注意力、記憶力和專注能力
        </p>
      </div>

      <div class="training-modules">
        <div class="module-card" @click="goToStroopTest">
          <div class="module-icon">
            <span class="material-symbols-outlined">palette</span>
          </div>
          <div class="module-content">
            <h3 class="module-title">斯特魯普測試</h3>
            <p class="module-description">
              訓練你的選擇性專注力和干擾抑制能力。在顏色與文字含義不一致的情況下，快速做出正確判斷。
            </p>
            <div class="module-features">
              <span class="feature-tag">選擇性專注</span>
              <span class="feature-tag">干擾抑制</span>
              <span class="feature-tag">反應速度</span>
            </div>
          </div>
          <div class="module-action">
            <mdui-button variant="filled">開始訓練</mdui-button>
          </div>
        </div>

        <div class="module-card" @click="goToMemoryCards">
          <div class="module-icon">
            <span class="material-symbols-outlined">memory</span>
          </div>
          <div class="module-content">
            <h3 class="module-title">記憶卡片配對</h3>
            <p class="module-description">
              鍛鍊你的工作記憶和視覺記憶能力。記住卡片位置，找出相同圖案的配對。
            </p>
            <div class="module-features">
              <span class="feature-tag">工作記憶</span>
              <span class="feature-tag">視覺記憶</span>
              <span class="feature-tag">空間記憶</span>
            </div>
          </div>
          <div class="module-action">
            <mdui-button variant="filled">開始訓練</mdui-button>
          </div>
        </div>

        <div class="module-card" @click="selectModule('observation')">
          <div class="module-icon">
            <span class="material-symbols-outlined">visibility</span>
          </div>
          <div class="module-content">
            <h3 class="module-title">圖像細節觀察</h3>
            <p class="module-description">
              提升你的持續性專注力和細節觀察能力。仔細觀察圖像，記住關鍵細節並回答問題。
            </p>
            <div class="module-features">
              <span class="feature-tag">持續專注</span>
              <span class="feature-tag">細節觀察</span>
              <span class="feature-tag">圖像記憶</span>
            </div>
          </div>
          <div class="module-action">
            <mdui-button variant="filled" disabled>即將推出</mdui-button>
          </div>
        </div>
      </div>

      <div class="training-tips">
        <h3 class="tips-title">訓練建議</h3>
        <div class="tips-grid">
          <div class="tip-card">
            <div class="tip-icon">
              <span class="material-symbols-outlined">schedule</span>
            </div>
            <h4>每日練習</h4>
            <p>建議每天練習 15-20 分鐘，持續性訓練效果更佳</p>
          </div>
          <div class="tip-card">
            <div class="tip-icon">
              <span class="material-symbols-outlined">trending_up</span>
            </div>
            <h4>循序漸進</h4>
            <p>從簡單難度開始，逐步挑戰更高難度的訓練</p>
          </div>
          <div class="tip-card">
            <div class="tip-icon">
              <span class="material-symbols-outlined">psychology</span>
            </div>
            <h4>專注環境</h4>
            <p>選擇安靜的環境，避免干擾，提高訓練效果</p>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script setup lang="ts">
import { useRouter } from 'vue-router'
import NavBar from '@/components/NavBar.vue'

const router = useRouter()

function goToStroopTest() {
  router.push('/focus-training/stroop-test')
}

function goToMemoryCards() {
  router.push('/focus-training/memory-cards')
}

function selectModule(module: string) {
  // 圖像細節觀察功能尚未實現
  console.log('功能開發中...')
}
</script>

<style scoped>
.focus-training-container {
  min-height: 100vh;
  background: var(--md3-surface);
}

.main-content {
  padding: 2rem 6.25rem;
  max-width: 1200px;
  margin: 0 auto;
}

.page-header {
  text-align: center;
  margin-bottom: 3rem;
}

.page-title {
  font-size: 2.5rem;
  font-weight: 700;
  color: var(--md3-on-surface);
  margin-bottom: 1rem;
}

.page-description {
  font-size: 1.1rem;
  color: var(--md3-on-surface-variant);
  max-width: 600px;
  margin: 0 auto;
  line-height: 1.6;
}

.training-modules {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
  gap: 2rem;
  margin-bottom: 3rem;
}

.module-card {
  background: var(--md3-surface);
  border: 1px solid var(--md3-outline-variant);
  border-radius: 12px;
  padding: 2rem;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.module-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.1);
  border-color: var(--md3-primary);
}

.module-icon {
  width: 60px;
  height: 60px;
  background: var(--md3-primary-container);
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: var(--md3-on-primary-container);
}

.module-icon .material-symbols-outlined {
  font-size: 2rem;
}

.module-content {
  flex: 1;
}

.module-title {
  font-size: 1.5rem;
  font-weight: 600;
  color: var(--md3-on-surface);
  margin-bottom: 1rem;
}

.module-description {
  color: var(--md3-on-surface-variant);
  line-height: 1.6;
  margin-bottom: 1.5rem;
}

.module-features {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
}

.feature-tag {
  background: var(--md3-secondary-container);
  color: var(--md3-on-secondary-container);
  padding: 0.25rem 0.75rem;
  border-radius: 16px;
  font-size: 0.8rem;
  font-weight: 500;
}

.module-action {
  text-align: center;
}

.training-area {
  background: var(--md3-surface);
  border: 1px solid var(--md3-outline-variant);
  border-radius: 12px;
  padding: 2rem;
  margin-bottom: 2rem;
}

.module-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 2rem;
  padding-bottom: 1rem;
  border-bottom: 1px solid var(--md3-outline-variant);
}

.module-content-area {
  min-height: 400px;
}

.training-tips {
  margin-top: 3rem;
}

.tips-title {
  font-size: 1.8rem;
  font-weight: 600;
  color: var(--md3-on-surface);
  text-align: center;
  margin-bottom: 2rem;
}

.tips-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 2rem;
}

.tip-card {
  background: var(--md3-surface-variant);
  border-radius: 12px;
  padding: 2rem;
  text-align: center;
  transition: transform 0.2s ease;
}

.tip-card:hover {
  transform: translateY(-2px);
}

.tip-icon {
  width: 50px;
  height: 50px;
  background: var(--md3-primary);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: var(--md3-on-primary);
  margin: 0 auto 1rem;
}

.tip-icon .material-symbols-outlined {
  font-size: 1.5rem;
}

.tip-card h4 {
  font-size: 1.2rem;
  font-weight: 600;
  color: var(--md3-on-surface);
  margin-bottom: 0.5rem;
}

.tip-card p {
  color: var(--md3-on-surface-variant);
  line-height: 1.5;
}

@media (max-width: 1024px) {
  .main-content {
    padding: 2rem;
  }
}

@media (max-width: 768px) {
  .main-content {
    padding: 1rem;
  }
  
  .page-title {
    font-size: 2rem;
  }
  
  .training-modules {
    grid-template-columns: 1fr;
  }
  
  .module-card {
    padding: 1.5rem;
  }
  
  .tips-grid {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 480px) {
  .page-header {
    margin-bottom: 2rem;
  }
  
  .page-title {
    font-size: 1.8rem;
  }
  
  .page-description {
    font-size: 1rem;
  }
  
  .module-card {
    padding: 1rem;
  }
  
  .module-icon {
    width: 50px;
    height: 50px;
  }
  
  .module-icon .material-symbols-outlined {
    font-size: 1.5rem;
  }
  
  .module-title {
    font-size: 1.3rem;
  }
}
</style>