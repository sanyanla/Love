<template>
  <div class="moments py-12">
    <div class="container mx-auto px-4">
      <h2 class="text-2xl text-center bg-gradient-to-r from-red-500 to-blue-500 bg-clip-text text-transparent mb-6">{{ momentTitle }}</h2>
      
      <!-- 加载状态 -->
      <div v-if="isLoading" class="flex justify-center items-center py-16">
        <div class="w-16 h-16 border-4 border-primary border-t-accent rounded-full animate-spin"></div>
      </div>
      
      <!-- 空状态 -->
      <div v-else-if="moments.length === 0" class="text-center text-gray-200 py-16">
        <p class="text-lg">暂无生活点滴</p>
      </div>
      
      <!-- 点点滴滴列表 -->
      <div v-else class="max-w-4xl mx-auto">
        <div class="space-y-8">
          <div 
            v-for="moment in moments" 
            :key="moment.id" 
            class="bg-white bg-opacity-90 backdrop-blur-sm rounded-xl shadow-lg p-8 transition-all duration-300 hover:shadow-xl"
          >
            <!-- 日期 -->
            <div class="text-gray-500 text-sm mb-2">{{ moment.date }}</div>
            
            <!-- 标题 -->
            <h3 class="text-xl font-bold text-gray-800 mb-4">{{ moment.title }}</h3>
            
            <!-- 内容 -->
            <div class="text-gray-600 leading-relaxed mb-6">
              {{ moment.content }}
            </div>
            
            <!-- 图片 -->
            <div v-if="moment.image" class="mb-4">
              <img :src="moment.image" alt="生活点滴" class="w-full rounded-lg shadow-md">
            </div>
            

          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { getMomentsFromWikiCloud, getConfigFromWikiCloud } from '../utils/api'

// 生活点滴数据
const moments = ref([])
const isLoading = ref(true)
const momentTitle = ref('💕世间最动情之事，莫过于两人相依💑，走过四季三餐的温暖。')

// 从维基云表格获取生活点滴数据
onMounted(async () => {
  isLoading.value = true
  try {
    // 获取配置数据
    const config = await getConfigFromWikiCloud()
    momentTitle.value = config.momentTitle || momentTitle.value
    
    // 获取生活点滴数据
    const data = await getMomentsFromWikiCloud()
    moments.value = data
  } catch (error) {
    console.error('获取数据失败:', error)
  } finally {
    isLoading.value = false
  }
})
</script>

