<template>
  <div>
    <div class="card">
      <div class="card-header">
        Naver
      </div>
      <div class="card-body">
        <div>
          <div id="naverIdLogin"></div>
        </div>
        <a href="#" class="btn btn-primary mt-1" type="button" v-if="naverlogin_flag" @click="logout">Log out</a>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      naverLogin: null,
      naverlogin_flag: false
    };
  },
  mounted() {
    this.naverLogin = new window.naver.LoginWithNaverId({
      clientId: "GpNJS9bgPn9m8oQdUbzV",                   //개발자센터에 등록한 ClientID
      callbackUrl: "http://localhost:8080/naverlogin",    //개발자센터에 등록한 callback Url
      isPopup: false ,                                                          //팝업을 통한 연동처리 여부
      loginButton: { color: "green", type: 3, height: 48,width:222 }, //로그인 버튼의 타입을 지정
    });
    //설정정보를 초기화하고 연동을 준비
    this.naverLogin.init();
    
    this.naverLogin.getLoginStatus((status) => {      
      if (status) {
        console.log(status);
        console.log(this.naverLogin.user);
        //필수적으로 받아야하는 프로필 정보가 있다면 callback처리 시점에 체크
        var email = this.naverLogin.user.getEmail();
        if (email == undefined || email == null) {
          alert("이메일은 필수정보입니다. 정보제공을 동의해주세요.");
          //사용자 정보 재동의를 위하여 다시 네아로 동의페이지로 이동함
          this.naverLogin.reprompt();
          return;
        }
          this.naverlogin_flag = true
          // alert("Log in success.");
      } else {
        console.log("callback 처리에 실패하였습니다.");
        this.naverlogin_flag = false
      }
    });
  },
  methods: {
    logout() {
      // service_provider
      const accessToken = this.naverLogin.accessToken.accessToken;      
      const url = `/oauth2.0/token?grant_type=delete&client_id=zFcLWPMTcDQTNTB6iIOy&client_secret=bUW7FZMpS9&access_token=${accessToken}&service_provider=NAVER`;
      // const url = `https://nid.naver.com/oauth2.0/token?grant_type=delete&client_id=zFcLWPMTcDQTNTB6iIOy&client_secret=bUW7FZMpS9&access_token=${accessToken}&service_provider=NAVER`;
      console.log(url)
      axios.get(url).then((res) => {
        this.naverlogin_flag = false
        console.log(res.data);
        // alert("Log out success.");
      });      
    },
  },
};
</script>

