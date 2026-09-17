<template>
  <div class="love-list py-12">
    <div class="container mx-auto px-4">
      <h2 class="text-2xl text-center bg-gradient-to-r from-red-500 to-blue-500 bg-clip-text text-transparent mb-6">{{ listTitle }}</h2>
      
      <!-- 加载状态 -->
      <div v-if="isLoading" class="flex justify-center items-center py-16">
        <div class="w-16 h-16 border-4 border-primary border-t-accent rounded-full animate-spin"></div>
      </div>
      
      <!-- 空状态 -->
      <div v-else-if="loveList.length === 0" class="text-center text-gray-200 py-16">
        <p class="text-lg">暂无爱情清单</p>
      </div>
      
      <!-- 爱情清单列表 -->
      <div v-else class="max-w-3xl mx-auto bg-white/50 bg-opacity-50 backdrop-blur-sm rounded-xl shadow-lg p-8">
        <ul class="space-y-4">
          <li 
            v-for="item in loveList" 
            :key="item.id" 
            class="flex items-center justify-between p-4 rounded-lg transition-all duration-300 hover:bg-gray-50"
          >
            <div class="flex items-center space-x-4">
              <!-- 完成状态复选框 -->
              <div class="flex-shrink-0">
                <div 
                  :class="['w-6 h-6 rounded-full flex items-center justify-center transition-all duration-300', 
                           item.todo ? 'bg-primary text-white shadow-md' : 'bg-gray-200 text-gray-500 hover:bg-gray-300']"
                >
                  <svg v-if="item.todo" class="w-4 h-4" fill="currentColor" viewBox="0 0 20 20">
                    <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd"></path>
                  </svg>
                </div>
              </div>
              
              <!-- 清单内容 -->
              <div :class="['flex-1 text-lg', item.todo ? 'text-gray-500 line-through' : 'text-gray-800']">
                {{ item.list }}
              </div>
            </div>
          </li>
        </ul>
        
        <!-- 统计信息 -->
        <div class="mt-8 pt-6 border-t border-gray-200 flex justify-between text-gray-600">
          <span>总计：<span class="text-red-500 font-bold">{{ loveList.length }}</span> 项</span>
          <span>完成：<span class="text-red-500 font-bold">{{ completedCount }}</span> 项</span>
          <span>剩下：<span class="text-red-500 font-bold">{{ loveList.length - completedCount }}</span> 项</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { getLoveListFromWikiCloud, getConfigFromWikiCloud } from '../utils/api'

// 爱情清单数据
const loveList = ref([])
const isLoading = ref(true)
const listTitle = ref('📜一百件事记录着我们的点点滴滴，你一百种样子💃，我一百种喜欢。')

// 从维基云表格获取爱情清单数据
onMounted(async () => {
  isLoading.value = true
  try {
    // 获取配置数据
    const config = await getConfigFromWikiCloud()
    listTitle.value = config.listTitle || listTitle.value
    
    // 获取爱情清单数据
    const data = await getLoveListFromWikiCloud()
    loveList.value = data
  } catch (error) {
    console.error('获取数据失败:', error)
  } finally {
    isLoading.value = false
  }
})

// 计算已完成的项数
const completedCount = computed(() => {
  return loveList.value.filter(item => item.todo).length
})
</script>

<style scoped>
/* LoveList组件样式 */
</style>
