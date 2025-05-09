<script setup>
import { computed } from 'vue'


const props = defineProps({
  currentPage: Number,
  totalPages: Number,
  maxVisible: {
    type: Number,
    default: 10
  }
})

const emit = defineEmits(['update:page'])

// 현재 페이지 기준으로 보여줄 페이지 번호 계산
const visiblePages = computed(() => {
  const half = Math.floor(props.maxVisible / 2)
  let start = Math.max(1, props.currentPage - half)
  let end = start + props.maxVisible - 1

  if (end > props.totalPages) {
    end = props.totalPages
    start = Math.max(1, end - props.maxVisible + 1)
  }

  return Array.from({ length: end - start + 1 }, (_, i) => start + i)
})
</script>

<template>
  <nav class="pagination" role="navigation" aria-label="페이지 네비게이션">
    <ul class="pagination-list">
      <!-- 첫 페이지 -->
      <li>
        <button
          type="button"
          class="page-btn page-nav"
          @click="emit('update:page', 1)"
          :disabled="currentPage === 1"
          aria-label="첫 페이지"
        >
          «
        </button>
      </li>

      <!-- 이전 페이지 -->
      <li>
        <button
          type="button"
          class="page-btn page-nav"
          @click="emit('update:page', currentPage - 1)"
          :disabled="currentPage === 1"
          aria-label="이전 페이지"
        >
          ‹
        </button>
      </li>

      <!-- 중간 페이지 목록 -->
      <li v-for="page in visiblePages" :key="page">
        <button
          type="button"
          class="page-btn page-nav"
          :class="{ active: page === currentPage }"
          :aria-current="page === currentPage ? 'page' : null"
          @click="emit('update:page', page)"
        >
          {{ page }}
        </button>
      </li>

      <!-- 다음 페이지 -->
      <li>
        <button
          type="button"
          class="page-btn page-nav"
          @click="emit('update:page', currentPage + 1)"
          :disabled="currentPage === totalPages"
          aria-label="다음 페이지"
        >
          ›
        </button>
      </li>

      <!-- 마지막 페이지 -->
      <li>
        <button
          type="button"
          class="page-btn page-nav"
          @click="emit('update:page', totalPages)"
          :disabled="currentPage === totalPages"
          aria-label="마지막 페이지"
        >
          »
        </button>
      </li>
    </ul>
  </nav>
</template>

<style scoped>
.pagination {
  text-align: center;
  margin-top: 8px;
}

.pagination-list {
  display: flex;
  justify-content: center;
}

.page-btn,
.page-nav {
  border: none;
  background-color: #fff;
  color: #333;
  padding: 8px 14px;
  margin: 0 3px;
  border-radius: 8px;
  cursor: pointer;
  transition: background-color 0.2s ease;
}

.page-btn:hover,
.page-nav:hover {
  background-color: #dcd5f2;
}

.page-btn.active {
  background-color: #b7a6e4;
  color: #fff;
  font-weight: 600;
}
</style>
