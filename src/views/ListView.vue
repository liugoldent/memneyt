<template>
  <div>
  目前共 {{  count  }} 筆資料
  </div>
  <div
    ref="scrollArea"
    class="scrollContainer"
    @scroll="handleScroll"
  >
    <ul v-for="(item, index) in items" :key="index">
      {{
        item
      }}
    </ul>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'

export default {
  setup() {
    const items = ref([])
    const currentPage = ref(1)
    const loadingStatus = ref(false)
    const scrollArea = ref(null)
    let count = ref(0)
    /**
     * @description 偵測是否滑到最下面，如果是要再去抓資料
     */
    const handleScroll = () => {
      const scrollHeight = scrollArea.value.scrollHeight
      const scrollTop = scrollArea.value.scrollTop
      const offsetHeight = scrollArea.value.offsetHeight

      if (scrollTop + offsetHeight >= scrollHeight && !loadingStatus.value) {
        fetchData()
      }
    }

    onMounted(() => {
      fetchData()
    })

    const fetchData = async function () {
      try {
        loadingStatus.value = true
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
          loadingStatus.value = false
          alert('查無資料')
          return
        }
        count.value = count.value + data.length
        data.forEach((item) => {
          items.value.push(item.name)
        })
        currentPage.value++
        loadingStatus.value = false
      } catch (error) {
        loadingStatus.value = false
        alert(error)
        console.error('Error fetching data:', error) // 处理请求失败的情况
      }
    }
    return {
      items,
      scrollArea,
      count,
      handleScroll
    }
  }
}
</script>

<style scoped>
ul {
  height: 50px;
  padding: 0;
}

.scrollContainer {
  border: 1px solid white;
  border-radius: 8px;
  padding: 20px;
  overflow-y: scroll;
  height: 400px;
}
</style>
