<script setup>
import logo from '@/assets/logo_img.png';
import { ref } from 'vue';
import { useUserInfo } from '@/stores/loginInfoState';
import { useRouter } from 'vue-router';
import axios from 'axios';

// 로그인 form에서 사용자 input값을 바인딩 할 때 사용.
// loginInfo.value에 로그인 정보 들어갈거임.
const loginInfo = ref({});

const { setUserData } = useUserInfo();

// 로그인 버튼 클릭 시 실행될 함수 정의
const router = useRouter();

const handlerLogin = () => {
  // loginInfo.value 객체를 URLSearchParams으로 변환
  const param = new URLSearchParams(loginInfo.value);

  axios.post('/api/loginProc.do', param).then((res) => { // 서버api를 param으로 받아왔으면
    // 응답된 res에서 data만 추출해서 이것을 data라고 선언
    const data = res.data;
    // data.result가 SUCCESS라면
    if (data.result === 'SUCCESS') {
      // 로그인된 userInfo를 sessionStorage.setItem에 json형태로 저장
      sessionStorage.setItem('userInfo', JSON.stringify(data));
      setUserData(data);
      // 그 뒤에 /vue로 이동
      router.push('/vue');
    } else {
      alert('아이디 혹은 비밀번호가 일치하지 않아요');
      return;
    }
  });
};
</script>

<template>
  <div class="login-main">
    <div class="login-container">
      <div class="login-image">
        <img alt="" :src="logo" />
      </div>
      <h3>안되는 것이 실패가 아니라 포기하는 것이 실패다</h3>
      <div>
        성공은 실패의 꼬리를 물고 온다.지금 포기한 것이 있는가 ?
        <br />
        그렇다면 다시 시작해 보자. <br />
        안되는 것이 실패가 아니라 포기하는 것이 실패다. <br />
        포기한 순간이 성공하기 5 분전이기 쉽다. <br />
        실패에서 더 많이 배운다. <br />
        실패를 반복해서 경험하면 실망하기 쉽다. <br />
        하지만 포기를 생각해선 안된다.실패는 언제나 중간역이지 종착역은 아니다. <br />
      </div>
      <div>-이대희, ‘1 % 의 가능성을 희망으로 바꾼 사람들’ 에서</div>
    </div>
    <div class="login-box">
      <div class="buttons inputs">
        <div>
          <label> 아이디 </label>
          <input v-model="loginInfo.lgn_Id" required />
        </div>
        <div>
          <label> 비밀번호 </label>
          <input v-model="loginInfo.pwd" required type="password" />
        </div>
        <div>
          <button class="login-button" @click="handlerLogin">Login</button>
          <button class="signup-button">Sign Up</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="css">
@import './styled.css';
</style>
