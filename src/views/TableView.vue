<template>
  <div ref="scrollArea" style="overflow-y: scroll; height: 400px" @scroll="handleScroll">
    <div v-for="(item, index) in items" :key="index">
      {{ item }}
    </div>
  </div>
</template>

<script>
import { ref, onMounted, getCurrentInstance } from 'vue'

export default {
  setup() {
    const items = ref([])
    const currentPage = ref(1)
    const loading = ref(false)
    const scrollArea = ref(null)
    const { proxy } = getCurrentInstance()
    /**
     * @description 獲取api資料
     */
    const fetchData = async () => {
      loading.value = true
      proxy.$axios
        .post(`/crawler/test/goodInfo/cross1020/list/bull`, {
          day1: '2024-03-01',
          currentPage: currentPage.value
        })
        .then((res) => {
          const { data } = res
          items.value.push(...data)
          console.log(res)
        })
        .catch((err) => {
          console.log(err)
        })
      currentPage.value++
      loading.value = false
    }
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

    return {
      items,
      loading,
      scrollArea,
      handleScroll
    }
  }
}
</script>
