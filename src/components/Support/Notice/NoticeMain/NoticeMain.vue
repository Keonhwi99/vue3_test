<script setup>
import PageNavigation from '@/components/common/PageNavigation.vue';
import axios from 'axios';
import { onMounted, ref, watch } from 'vue';
import { useRoute } from 'vue-router';
import NoticeModal from '../NoticeModal/NoticeModal.vue';
import { useModalState } from '@/stores/modalState';

const route = useRoute();
// console.log('route', route);
const noticeList = ref([]);
const noticeCount = ref(0);
const modalState = useModalState();

const noticeSearch = (cPage = 1) => {
  const param = new URLSearchParams(route.query);
  param.append('currentPage', cPage);
  param.append('pageSize', 5);

  // param 객체를 가져온다.
  axios.post('/api/support/noticeListBody.do', param).then((res) => {
    // console.log(res.data);
    noticeList.value = res.data.list;
    noticeCount.value = res.data.count;
  });
};

// title 클릭하면 noticeDetail 열어줄거임 (모달창)
const noticeDetail = () => {
  modalState.$patch({ isOpen: true });
};

// route.query를 지켜볼 건데, 얘가 바뀌면 noticeSearch()를 바꿔줘라.
watch(
  () => {
    return route.query;
  },
  () => {
    noticeSearch();
  },
);

// onMounted: noticeMain이 켜지자마자 시작하는 hook
onMounted(() => {
  noticeSearch();
});
</script>

<template>
  <div class="notice-main-container">
    <table class="notice-table">
      <thead class="notice-table-header">
        <tr>
          <th>공지번호</th>
          <th>공지 제목</th>
          <th>공지 날짜</th>
          <th>작성자</th>
        </tr>
      </thead>
      <tbody>
        <template v-if="noticeCount > 0">
          <tr v-for="notice in noticeList" :key="notice.noticeId" class="notice-table-row">
            <td class="notice-cell">{{ notice.noticeId }}</td>
            <td class="notice-cell cursor-pointer hover:underline" @click="noticeDetail">
              {{ notice.noticeTitle }}
            </td>
            <td class="notice-cell">{{ notice.regDate.substring(0, 10) }}</td>
            <td class="notice-cell">{{ notice.loginId }}</td>
          </tr>
        </template>
        <template v-else>
          <tr>
            <td colspan="4" class="notice-empty-row">일치하는 검색 결과가 없습니다</td>
          </tr>
        </template>
      </tbody>
    </table>
    <PageNavigation :total-items="noticeCount" :items-per-page="5" :on-page-change="noticeSearch" />
  </div>
  <NoticeModal v-if="modalState.isOpen" />
</template>

<style>
@import './styled.css';
</style>
