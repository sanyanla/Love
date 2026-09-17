<template>
  <div class="container mx-auto px-4 py-8">
    <div class="max-w-4xl mx-auto">
      <!-- 页面标题 -->
      <div class="text-center mt-12 mb-12">
        <h1 class="text-2xl text-center bg-gradient-to-r from-red-500 to-blue-500 bg-clip-text text-transparent mb-6">{{ aboutTitle }}</h1>    
      </div>

      <!-- 情侣介绍 -->
      <div class="bg-white rounded-2xl shadow-lg p-8 mb-8">
        <div class="flex flex-col md:flex-row items-center gap-8">
          <!-- 男生头像 -->
          <div class="w-24 h-24 bg-gradient-to-br from-primary to-accent rounded-full flex items-center justify-center overflow-hidden hidden md:flex">
            <Avatar 
              :src="boyAvatar" 
              alt="男孩头像" 
              avatarClass="w-full h-full object-cover"
              placeholderSrc="/images/lazyload.svg"
            />
          </div>
          
          <!-- 情侣信息 -->
          <div class="flex-1 text-center">
            <h2 class="text-2xl font-bold text-red-500 mb-2">{{ boyName }} & {{ girlName }}</h2>
            <p class="text-gray-600 mb-4">相遇于 {{ meetDate }}，相爱于 {{ loveDate }}</p>
            <div class="flex justify-center items-center space-x-4 text-sm text-gray-500">
              <span>💖 相爱 {{ loveDays }} 天</span>
              <span>💞 相遇 {{ meetDays }} 天</span>
            </div>
          </div>
          
          <!-- 女生头像 -->
          <div class="w-24 h-24 bg-gradient-to-br from-secondary to-purple-400 rounded-full flex items-center justify-center overflow-hidden hidden md:flex">
            <Avatar 
              :src="girlAvatar" 
              alt="女孩头像" 
              avatarClass="w-full h-full object-cover"
              placeholderSrc="/images/lazyload.svg"
            />
          </div>
        </div>
      </div>

      <!-- 爱情宣言 -->
      <div class="bg-gradient-to-r from-primary to-accent rounded-2xl p-8 text-white mb-8 text-center">
        <h3 class="text-2xl font-bold mb-4">我们的爱情宣言</h3>
        <p class="text-lg leading-relaxed whitespace-pre-line">
          {{ loveDeclaration }}
        </p>
      </div>


    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'
import { getConfigFromWikiCloud } from '../utils/api'
import Avatar from './Avatar.vue'

export default {
  name: 'About',
  components: {
    Avatar
  },
  setup() {
    const boyName = ref('小明')
    const girlName = ref('小红')
    const boyAvatar = ref('')
    const girlAvatar = ref('')
    const meetDate = ref('2023-01-01')
    const loveDate = ref('2023-02-14')
    const loveDays = ref(0)
    const meetDays = ref(0)
    const loveDeclaration = ref('愿有岁月可回首，且以深情共白头。\n在时光的长河里，我们携手同行，\n用爱书写属于我们的浪漫篇章。')
    const aboutTitle = ref('我们的爱情故事💑从这里开始')

    const calculateDays = (startDate) => {
      const start = new Date(startDate)
      const today = new Date()
      const diffTime = Math.abs(today - start)
      return Math.ceil(diffTime / (1000 * 60 * 60 * 24))
    }

    onMounted(async () => {
      try {
        const config = await getConfigFromWikiCloud()
        boyName.value = config.boyName || boyName.value
        girlName.value = config.girlName || girlName.value
        boyAvatar.value = config.boyAvatar || ''
        girlAvatar.value = config.girlAvatar || ''
        meetDate.value = config.meetDate || config.meet || meetDate.value
        loveDate.value = config.loveDate || loveDate.value
        loveDeclaration.value = config.sayLove || loveDeclaration.value
        aboutTitle.value = config.aboutTitle || aboutTitle.value
      } catch (error) {
        console.error('获取配置失败:', error)
      }
      
      loveDays.value = calculateDays(loveDate.value)
      meetDays.value = calculateDays(meetDate.value)
    })

    return {
      boyName,
      girlName,
      boyAvatar,
      girlAvatar,
      meetDate,
      loveDate,
      loveDays,
      meetDays,
      loveDeclaration,
      aboutTitle
    }
  }
}
</script>