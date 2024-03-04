<template>
  <!-- <div ref="scrollArea" style="overflow-y: scroll; height: 400px" @scroll="handleScroll">
    <div v-for="(item, index) in items" :key="index">
      {{ item }}
    </div>
  </div> -->
  <div v-if="false" class="scroll-template">
    <div class="table-container" ref="tableContainer">
      <table>
        <thead>
          <tr>
            <th>股票代碼</th>
            <th>名稱</th>
            <th>收盤價</th>
            <th>成交量</th>
            <th>更新日期</th>
            <th>買賣</th>
            <th>10日偏差</th>
            <th>20日偏差</th>
          </tr>
        </thead>
      </table>
    </div>
    <div
      class="scroll-container"
      ref="scrollArea"
      style="overflow-y: auto; height: 250px"
      @scroll="handleScroll"
    >
      <table>
        <tbody>
          <tr v-for="(item, index) in items" :key="index">
            <td>{{ item.code }}</td>
            <td>{{ item.name }}</td>
            <td>{{ item.close }}</td>
            <td>{{ item.volume }}</td>
            <td>{{ item.updateDay }}</td>
            <td>{{ item.buyOrSell }}</td>
            <td>{{ item.bias10 }}</td>
            <td>{{ item.bias20 }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>

  <div class="scroll-container" ref="scrollArea" @scroll="handleScroll">
    <table class="custom-table">
      <thead class="sticky-header">
        <tr>
          <th>股票代碼</th>
          <th>名稱</th>
          <th>收盤價</th>
          <th>更新日期</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in items" :key="index">
          <td>{{ item.code }}</td>
          <td>{{ item.name }}</td>
          <td>{{ item.close }}</td>
          <td>{{ item.updateDay }}</td>
        </tr>
      </tbody>
    </table>
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
      console.log('1')
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

<style scoped>
.table-container {
  overflow: hidden;
}

.scroll-container {
  overflow-y: auto;
  height: 200px; /* 设置滚动容器的高度 */
}

.custom-table {
  width: 100%; /* 设置表格宽度为100% */
  table-layout: fixed; /* 设置表格布局方式为固定布局 */
}

th,
td {
  width: 180px; /* 设置 th 和 td 的宽度 */
  text-align: left; /* 设置文本居中对齐 */
}
.sticky-header {
  position: sticky;
  top: 0;
  z-index: 1; /* 确保表头位于内容之上 */
}
</style>
