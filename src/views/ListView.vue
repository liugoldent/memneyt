<template>
  <div ref="scrollArea" style="overflow-y: scroll; height: 400px" @scroll="handleScroll">
    <ul v-for="(item, index) in items" :key="index">
      {{ item }}
    </ul>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'

export default {
  setup() {
    const items = ref([])
    const currentPage = ref(1)
    const loading = ref(false)
    const scrollArea = ref(null)
    /**
     * @description 偵測是否滑到最下面，如果是要再去抓資料
     */
    const handleScroll = () => {
      const scrollHeight = scrollArea.value.scrollHeight
      const scrollTop = scrollArea.value.scrollTop
      const offsetHeight = scrollArea.value.offsetHeight

      if (scrollTop + offsetHeight >= scrollHeight && !loading.value) {
        fetchData()
      }
    }

    onMounted(() => {
      fetchData()
    })

    const fetchData = async function () {
      try {
        const res = await fetch(
          `https://api.github.com/users/yyx990803/repos?per_page=10&page=${currentPage.value}`,
          {
            method: 'GET'
          }
        )

        if (!res.ok) {
          throw new Error(`Network response was not ok: ${res.status}`)
        }

        const data = await res.json()

        if (data.length === 0) {
          alert('查無資料')
          return
        }

        data.forEach((item) => {
          items.value.push(item.full_name)
        })

        currentPage.value++
      } catch (error) {
        console.error('Error fetching data:', error) // 处理请求失败的情况
      }
    }
    return {
      items,
      loading,
      scrollArea,
      handleScroll
    }
  }
}
</script>

<style scoped>
ul {
  height: 50px;
}

</style>
