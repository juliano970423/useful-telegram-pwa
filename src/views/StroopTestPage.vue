<template>
  <div class="stroop-test-page">
    <NavBar />
    
    <main class="main-content">
      <div class="page-header">
        <h1 class="page-title">斯特魯普測試</h1>
        <p class="page-description">
          訓練你的選擇性專注力和干擾抑制能力
        </p>
      </div>
      
      <mdui-card class="test-card" variant="filled">
        <div class="test-content">
          <StroopTest />
        </div>
      </mdui-card>
      
      <div class="navigation-footer">
        <mdui-button variant="text" @click="goBack">
          <span class="material-symbols-outlined">arrow_back</span>
          返回專注力訓練
        </mdui-button>
        <mdui-button variant="filled" @click="goToMemoryCards">
          下一個訓練
          <span class="material-symbols-outlined">arrow_forward</span>
        </mdui-button>
      </div>
    </main>
  </div>
</template>

<script setup lang="ts">
import { useRouter } from 'vue-router'
import NavBar from '@/components/NavBar.vue'
import StroopTest from '@/components/StroopTest.vue'

const router = useRouter()

function goBack() {
  router.push('/focus-training')
}

function goToMemoryCards() {
  router.push('/focus-training/memory-cards')
}
</script>

<style scoped>
.stroop-test-page {
  min-height: 100vh;
  background: var(--md3-surface-variant);
}

.main-content {
  padding: 2rem 6.25rem;
  max-width: 800px;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.page-header {
  text-align: center;
  margin-bottom: 2rem;
}

.page-title {
  font-size: 2rem;
  font-weight: 600;
  color: var(--md3-on-surface);
  margin-bottom: 0.5rem;
}

.page-description {
  font-size: 1rem;
  color: var(--md3-on-surface-variant);
  margin: 0;
}

.test-card {
  padding: 2rem;
  background: var(--md3-surface);
  border-radius: 16px;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
  width: 100%;
  max-width: 600px;
}

.test-content {
  margin-bottom: 2rem;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.navigation-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 1rem;
  width: 100%;
  max-width: 600px;
  margin-top: 2rem;
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
  
  .test-card {
    padding: 1.5rem;
  }
  
  .page-title {
    font-size: 1.8rem;
  }
  
  .navigation-footer {
    flex-direction: column;
  }
  
  .navigation-footer mdui-button {
    width: 100%;
    justify-content: center;
  }
}

@media (max-width: 480px) {
  .main-content {
    padding: 0.5rem;
  }
  
  .test-card {
    padding: 1rem;
    border-radius: 12px;
  }
  
  .page-title {
    font-size: 1.6rem;
  }
  
  .page-description {
    font-size: 0.9rem;
  }
}
</style>