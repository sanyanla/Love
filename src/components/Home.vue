<template>
  <div class="home py-12">
    <div class="container mx-auto px-4">
      <!-- 情侣信息和倒计时 -->
        
        <!-- 恋爱时间倒计时 -->
        <div class="mt-1 text-center">
          <div class="text-2xl bg-gradient-to-r from-red-500 to-blue-500 bg-clip-text text-transparent mb-2">我们风雨同舟已经一起走过</div>
          <div class="text-2xl font-bold bg-gradient-to-r from-red-500 to-blue-500 bg-clip-text text-transparent mb-2">{{ formattedLoveTime }}</div>
        </div>
        
        <!-- 结婚周年倒计时 -->
        <div v-if="weddingDate" class="mt-4 text-center">
          <div class="text-gray-600">
            距离
            <span class="font-bold">结婚</span>
            <span class="text-red-500 font-bold">{{ weddingAnniversary }}</span>
            周年纪念日还有
            <span class="text-red-500 font-bold">{{ daysUntilWeddingAnniversary }}</span>
            天
          </div>
        </div>
        
        <!-- 重要日期倒计时 -->
        <div v-if="importantDays.length > 0" class="mt-1 text-center">
          <div v-for="(day, index) in importantDays" :key="index" class="text-gray-600 mb-2">
            距离
            <span class="font-bold">{{ day.name }}</span>
            还有
            <span class="text-red-500 font-bold">{{ day.daysRemaining }}</span>
            天
          </div>
        </div>
        
        <!-- 功能卡片 -->
        <div class="mt-8 grid grid-cols-1 md:grid-cols-3 gap-8 max-w-5xl mx-auto">
          <!-- 祝福墙卡片 -->
          <router-link to="/message" class="bg-white rounded-xl shadow-lg p-8 hover:shadow-xl transition-shadow duration-300">
            <div class="flex items-center space-x-6">
              <img src="/images/bless.svg" alt="祝福墙" class="w-12 h-12 text-primary">
              <div>
                <h3 class="text-lg font-bold text-gray-800">祝福墙</h3>
                <p class="text-sm text-gray-500">💌写下对我们的祝福吧</p>
              </div>
            </div>
          </router-link>
          
          <!-- 幸福路卡片 -->
          <router-link to="/timeline" class="bg-white rounded-xl shadow-lg p-8 hover:shadow-xl transition-shadow duration-300">
            <div class="flex items-center space-x-6">
              <img src="/images/time.svg" alt="幸福路" class="w-12 h-12 text-primary">
              <div>
                <h3 class="text-lg font-bold text-gray-800">幸福路</h3>
                <p class="text-sm text-gray-500">📜记录一起的美好时光</p>
              </div>
            </div>
          </router-link>
          
          <!-- 相册卡片 -->
          <router-link to="/gallery" class="bg-white rounded-xl shadow-lg p-8 hover:shadow-xl transition-shadow duration-300">
            <div class="flex items-center space-x-6">
              <img src="/images/photo.svg" alt="相册" class="w-12 h-12 text-primary">
              <div>
                <h3 class="text-lg font-bold text-gray-800">相册</h3>
                <p class="text-sm text-gray-500">📷留住我们的瞬间</p>
              </div>
            </div>
          </router-link>
          
          <!-- 点点滴滴卡片 -->
          <router-link to="/moments" class="bg-white rounded-xl shadow-lg p-8 hover:shadow-xl transition-shadow duration-300">
            <div class="flex items-center space-x-6">
              <img src="/images/shuoshuo.svg" alt="点点滴滴" class="w-12 h-12 text-primary">
              <div>
                <h3 class="text-lg font-bold text-gray-800">点点滴滴</h3>
                <p class="text-sm text-gray-500">📝记录你我生活的点滴</p>
              </div>
            </div>
          </router-link>
          
          <!-- 爱情清单卡片 -->
          <router-link to="/love-list" class="bg-white rounded-xl shadow-lg p-8 hover:shadow-xl transition-shadow duration-300">
            <div class="flex items-center space-x-6">
              <img src="/images/loveList.svg" alt="爱情清单" class="w-12 h-12 text-primary">
              <div>
                <h3 class="text-lg font-bold text-gray-800">爱情清单</h3>
                <p class="text-sm text-gray-500">📋爱情一步步</p>
              </div>
            </div>
          </router-link>
          
          <!-- 关于我们卡片 -->
          <router-link to="/about" class="bg-white rounded-xl shadow-lg p-8 hover:shadow-xl transition-shadow duration-300">
            <div class="flex items-center space-x-6">
              <img src="/images/about.svg" alt="关于我们" class="w-12 h-12 text-primary">
              <div>
                <h3 class="text-lg font-bold text-gray-800">关于我们</h3>
                <p class="text-sm text-gray-500">💏我们的经历</p>
              </div>
            </div>
          </router-link>
        </div>     

      


    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed, onUnmounted } from 'vue'
import { getConfigFromWikiCloud } from '../utils/api'

// 配置数据
const config = ref({})

// 恋爱开始日期
const loveDate = ref('2023-01-15')

// 结婚日期
const weddingDate = ref('2015-03-03')



// 精选照片
const featuredPhotos = ref([
  '/images/01.png',
  '/images/02.png',
  '/images/03.png',
  '/images/04.png',
  '/images/05.png'
])

// 爱情宣言
const loveDeclaration = ref('我能想到最浪漫的事，就是和你一起慢慢变老。')



// 重要日期倒计时
const importantDays = ref([])

// 计算恋爱时间（精确到秒）
const calculateLoveTime = () => {
  const today = new Date()
  const startDate = new Date(loveDate.value)
  
  // 计算年
  const years = today.getFullYear() - startDate.getFullYear()
  
  // 计算月
  let months = today.getMonth() - startDate.getMonth()
  if (today.getDate() < startDate.getDate()) {
    months--
  }
  if (months < 0) {
    months += 12
  }
  
  // 计算日
  const tempDate = new Date(today)
  tempDate.setMonth(today.getMonth() - 1)
  let days = 0
  if (today.getDate() >= startDate.getDate()) {
    days = today.getDate() - startDate.getDate()
  } else {
    days = startDate.getDate() - today.getDate()
    tempDate.setDate(0)
    days = tempDate.getDate() - days
  }
  
  // 计算时、分、秒
  const timeDiff = today.getTime() - startDate.getTime()
  const totalSeconds = Math.floor(timeDiff / 1000)
  const hours = Math.floor(totalSeconds % (3600 * 24) / 3600)
  const minutes = Math.floor(totalSeconds % 3600 / 60)
  const seconds = Math.floor(totalSeconds % 60)
  
  return {
    years,
    months,
    days,
    hours,
    minutes,
    seconds
  }
}

// 恋爱时间对象
const loveTime = ref(calculateLoveTime())

// 计算结婚周年相关信息
const calculateWeddingAnniversary = () => {
  const today = new Date()
  const wedding = new Date(weddingDate.value)
  
  // 计算当前结婚周年数
  const currentYear = today.getFullYear()
  const weddingYear = wedding.getFullYear()
  let anniversary = currentYear - weddingYear
  
  // 检查今年的结婚纪念日是否已经过了
  const thisYearsAnniversary = new Date(currentYear, wedding.getMonth(), wedding.getDate())
  if (today < thisYearsAnniversary) {
    anniversary--
  }
  
  // 计算下一个结婚纪念日
  let nextAnniversaryDate = new Date(currentYear, wedding.getMonth(), wedding.getDate())
  if (today > nextAnniversaryDate) {
    nextAnniversaryDate.setFullYear(currentYear + 1)
  }
  
  // 计算距离下一个结婚纪念日的天数
  const timeDiff = nextAnniversaryDate.getTime() - today.getTime()
  const daysUntilNext = Math.ceil(timeDiff / (1000 * 3600 * 24))
  
  return {
    anniversary,
    daysUntilNext
  }
}

// 结婚周年信息
const weddingInfo = ref(calculateWeddingAnniversary())

// 计算属性：当前结婚周年数
const weddingAnniversary = computed(() => {
  return weddingInfo.value.anniversary + 1 // +1 表示下一个周年
})

// 计算属性：距离下一个结婚周年的天数
const daysUntilWeddingAnniversary = computed(() => {
  return weddingInfo.value.daysUntilNext
})

// 定时器
let timer = null

onMounted(async () => {
  // 从维基云表格获取配置
  const fetchedConfig = await getConfigFromWikiCloud()
  config.value = fetchedConfig
  
  // 更新恋爱开始日期
  if (config.value.loveDate) {
    loveDate.value = config.value.loveDate
  }
  
  // 更新结婚日期
  if (config.value.weddingDate) {
    weddingDate.value = config.value.weddingDate
  }
  

  
  // 更新精选照片
  if (config.value.featuredPhotos) {
    try {
      const photos = JSON.parse(config.value.featuredPhotos)
      if (Array.isArray(photos)) {
        featuredPhotos.value = photos
      }
    } catch (e) {
      console.error('解析精选照片失败:', e)
    }
  }
  
  // 更新爱情宣言
  if (config.value.sayLove) {
    loveDeclaration.value = config.value.sayLove
  }
  
  // 更新重要日期倒计时
  if (config.value.importantDay) {
    // 解析重要日期
    const importantDaysData = config.value.importantDay.split(';').map(item => {
      const [name, dateStr] = item.split(',').map(str => str.trim())
      return { name, dateStr }
    }).filter(item => item.name && item.dateStr)
    
    // 计算每个重要日期的剩余天数
    const today = new Date()
    const currentYear = today.getFullYear()
    
    const calculatedDays = importantDaysData.map(day => {
      const [month, dayOfMonth] = day.dateStr.split('-').map(Number)
      let targetDate = new Date(currentYear, month - 1, dayOfMonth)
      
      // 如果今年的日期已经过了，计算明年的
      if (today > targetDate) {
        targetDate = new Date(currentYear + 1, month - 1, dayOfMonth)
      }
      
      // 计算剩余天数
      const timeDiff = targetDate.getTime() - today.getTime()
      const daysRemaining = Math.ceil(timeDiff / (1000 * 3600 * 24))
      
      return {
        name: day.name,
        daysRemaining
      }
    })
    
    importantDays.value = calculatedDays
  }
  
  // 每秒更新一次数据
  timer = setInterval(() => {
    loveTime.value = calculateLoveTime()
    weddingInfo.value = calculateWeddingAnniversary()
  }, 1000)
})

// 组件卸载时清除定时器
onUnmounted(() => {
  if (timer) {
    clearInterval(timer)
  }
})

// 格式化显示时间
const formattedLoveTime = computed(() => {
  const { years, months, days, hours, minutes, seconds } = loveTime.value
  return `${years}年${months}月${days}日${hours.toString().padStart(2, '0')}时${minutes.toString().padStart(2, '0')}分${seconds.toString().padStart(2, '0')}秒`
})
</script>


