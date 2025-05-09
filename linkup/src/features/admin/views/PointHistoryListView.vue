<script setup>
import { ref } from 'vue'
import AdminListTemplate from '@/features/admin/components/AdminListTemplate.vue'
import { fetchPointTransactionList } from '@/api/admin.js'  // 실제 API 연동
import { format } from 'date-fns'

const pageTitle = '포인트 내역 조회';

// 필터 상태 관리
const filters = ref({
  userId: '',
  authority: '',
  transactionType: '',
  startDate: '',
  endDate: ''
});

const page = ref(1); // 페이지 상태 관리
const rows = ref([]); // 데이터 저장
const totalPages = ref(1); // 전체 페이지 수


// 포인트 내역 조회 함수
// 포인트 내역 조회 함수
const fetchList = async ({ page = 1 }) => {
  try {
    const params = {
      userId: filters.value.userId || '',
      roleName: filters.value.authority || '',
      transactionType: filters.value.transactionType || '',
      startDate: filters.value.startDate || '',
      endDate: filters.value.endDate || '',
      page
    };

    // 빈 값 제거
    Object.keys(params).forEach(key => {
      if (!params[key]) delete params[key]
    });

    const res = await fetchPointTransactionList(params);
    console.log('응답 데이터:', res);

    return {
      data: res.data?.data?.content || [],
      totalPages: res.data?.data?.totalPages || 1
    };
  } catch (e) {
    console.error('API 요청 실패:', e);
    return { data: [], totalPages: 1 };
  }
};


// 테이블 컬럼 설정
const columns = [
  { key: 'pointTransactionId', label: 'ID' },
  { key: 'userId', label: '사용자 ID' },
  { key: 'userName', label: '사용자 이름' },
  { key: 'roleName', label: '권한' },
  {
    key: 'amount',
    label: '금액',
    format: v => (v != null ? `${v > 0 ? '+' : ''}${v.toLocaleString()}` : '-')
  },
  { key: 'transactionType', label: '유형' },
  {
    key: 'createdAt',
    label: '일시',
    format: v => v ? format(new Date(v), 'yyyy-MM-dd HH:mm') : '-'
  }
];
</script>

<template>
  <div>
    <AdminListTemplate
      :fetchFn="fetchList"
      :columns="columns"
      :initFilters="filters"
      :pageTitle="pageTitle"
      :enableModal="false"
    >

    <!-- 필터 영역 -->
      <template #filters="{ filters }">
        <!-- 사용자 ID 필터 -->
        <label class="filter-label" for="user-id-input">사용자 ID:</label>
        <input
          id="user-id-input"
          v-model="filters.userId"
          class="select-box id-input"
          placeholder="ID"
        />

        <!-- 권한 필터 -->
        <label class="filter-label" for="status-select">권한:</label>
        <select
          id="status-select"
          v-model="filters.authority"
          class="select-box"
        >
          <option value="">전체</option>
          <option value="USER">회원</option>
          <option value="OWNER">사업자</option>
        </select>

        <!-- 트랜잭션 유형 필터 -->
        <label class="filter-label" for="transaction-type">트랜잭션 유형:</label>
        <select v-model="filters.transactionType" id="transaction-type" class="select-box">
          <option value="">전체</option>
          <option value="CHARGE">충전</option>
          <option value="PAYMENT">지불</option>
          <option value="REFUND">반환</option>
          <option value="WITHDRAW">환불</option>
        </select>

        <!-- 조회 기간 필터 -->
        <label class="filter-label">조회 기간:</label>
        <input type="date" v-model="filters.startDate" class="select-box date-input" />
        ~
        <input type="date" v-model="filters.endDate" class="select-box date-input" />
      </template>
    </AdminListTemplate>
    <!-- 테이블 데이터가 없을 경우 처리 -->
  </div>
</template>

<style scoped>
/* 여기에 스타일을 추가할 수 있습니다 */
</style>


<style scoped>
.filter-wrapper {
  display: flex;
  margin-bottom: 12px;
  justify-content: space-between;
}

.filter-box {
  display: flex;
  justify-content: flex-end;
  align-items: center;
  gap: 15px;
}

.filter-label {
  display: flex;
  align-items: center;
  font-size: 14px;
  gap: 6px;
}

.select-box {
  margin-left: 12px;
  padding: 6px 10px;
  font-size: 14px;
  border: 1px solid #ccc;
  border-radius: 6px;
  background-color: #fff;
  color: #333;
}

.filter-fields {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  align-items: center;
  border: 0;
  padding: 0;
  margin: 0;
}

.input-box {
  min-width: 160px;
}

select,
input[type="text"] {
  height: 32px;
  padding: 4px 10px;
  font-size: 14px;
  border: 1px solid #ccc;
  border-radius: 6px;
  background-color: #fff;
  color: #333;
}

select:focus,
input[type="text"]:focus {
  outline: none;
  border-color: #7d6fb3;
  box-shadow: 0 0 0 2px rgba(125, 111, 179, 0.2);
}

.id-input {
  width: 50px;
}

/* 스크린리더 전용 */
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
</style>
