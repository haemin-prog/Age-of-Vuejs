<template>
  <div>
    {{ str }}
    <!-- 컴포넌트 사용 -->
    <!-- <컴포넌트 이름 v-bind: 프롭스 속성 이름="상위 컴포넌트의 데이터 이름"></컴포넌트 이름> -->
    <!-- v-on: (이벤트를 전달할)컴포넌트 메소드 이름 = "실행할 메소드 이름" -->
    <app-header v-bind:propsdata="str" v-on:renew="renewStr"></app-header>
  </div>
</template>

<script>
// import 를 이용해 컴포넌트를 등록
import AppHeader from "./components/AppHeader.vue";

export default {
  // 여러 개의 컴포넌트에서 동일한 값을 사용(참조)하면 안되기에
  // 새로운 객체는 function()의 return을 이용해야 함
  data: function() {
    return {
      str: "props test",
    };
  },
  // 컴포넌트 등록/사용
  components: {
    "app-header": AppHeader,
  },
  methods: {
    renewStr: function() {
      this.str = "New " + this.str;
    },
  },
};
</script>
<style></style>
