<template>
<div>
	<fut-head v-if="mapView" :style="`height: ${height}vh`" :propImg="stadiumImg"/>
  <fut-map v-else :propSearchWord="`${selectMatch.stadiumname} 풋살 경기장`"
    :propRoadView="true" :propRightClick="true"
    :style="`height: ${height}vh; width:100%;`"></fut-map>
  <v-card class="card">
    <v-card-title><h1>
      <router-link :to="{name: 'futsalstadium', params: {stadiumName: selectMatch.stadiumname}}">
        {{selectMatch.stadiumname}}</router-link>
    </h1></v-card-title>
    <v-card-subtitle>{{selectMatch.stadiumaddr}}<br/>{{fnc.timeToDateWeek(selectMatch.time)}}</v-card-subtitle>
    <v-card>
      <v-chip outlined @click="fnc.linkCopy($route.fullPath)">주소복사하기</v-chip>
      <v-chip outlined @click="viewTogle()">지도보기</v-chip>
    </v-card>
    <v-card-text outlined><h5>{{moveResult}}</h5></v-card-text>
    <v-card-text>{{selectMatch.stadiumname}} {{fnc.timeToDate(selectMatch.time)}} 의 경기는
        <code>{{success}}%</code> 확률로 정상 진행되고 있습니다.
    </v-card-text>
  </v-card>
  <v-card class="card">
    <v-card-title>특이사항</v-card-title>
    <v-card-subtitle>{{difficultyMsg[selectMatch.difficulty-1]}}</v-card-subtitle>
    <v-row class="justify-center pa-1">
      <v-col
        v-for="n of matchRule"
        :key="n" cols="2">
        <v-card style="height:100%">
          <v-img :src="require(`@/assets/img/matchRule/${n+([2,3].includes(n) ? '.png' : '.svg')}`)"/>
          <v-card-text class="text-center">{{msgSwitch(n)}}</v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-card>
  <v-divider/>
  <v-card class="card">
    <h3>구장 시설</h3>
    <v-row class="justify-center pa-1">
      <v-col
        v-for="n of stadiumFacility"
        :key="n" cols="2">
        <v-card>
          <v-img :src="require(`@/assets/img/stadium/${n.slice(0,-1)}.svg`)"
            :style="`opacity: ${n.slice(-1)!= 0 ? 1 : 0.3};`"/>
        </v-card>
      </v-col>
    </v-row>
  </v-card>
  <v-card class="card">
    <h4>구장 특이사항</h4>
    <li justify-left 
      v-for="item of stadiumText" :key="item">
      {{item}}
    </li>
  </v-card>
  <v-divider/>
  <v-card class="card">
    <div class="d-flex">
      <v-img elevation-6 src="https://pds.joins.com/news/component/htmlphoto_mmdata/201910/26/ce877ed2-0800-457f-b9a6-a86044718d40.jpg"/>
      <div>
        <v-card-title>매니저  : {{selectMatch.adminname}}</v-card-title>
        <v-card-subtitle>한분한분 같이 웃고 즐기며 소통하는 매니저 {{selectMatch.adminname}}입니다. 
                매 경기 안 다치고 소중한 시간 재미있게 즐길수 있게 최선을 다하겠습니다. 
                같이 플랩으로 오세요!</v-card-subtitle>
      </div>
    </div>
  </v-card>
  <v-divider/>
  <v-card class="card">   
    <ul>
      <h2>- 주의사항 -</h2>
      <span>풋볼 매치는 참가자 간의 신뢰를 바탕으로 진행됩니다.</span>
      <li>다른 참가자들을 위해 시간을 준수해 주세요.</li>
      <li>풋살화 또는 스터드가 없는 운동화를 착용해주세요.</li>
      <li>불필요한 언행, 지시 등은 삼가해주세요.</li>
      <li>과도한 경쟁, 승부욕은 나와 상대방의 부상을 야기할 수 있습니다.</li>
      <li>폭언, 폭행은 이용 정지의 심각한 사유가 될 수 있습니다.</li>
    </ul>
    <ul>
      <h4>이용 제한이 되는 경우</h4>
      <li>경기 시작 1시간 30분 전까지 신청 취소 없이 무단 불참할 경우</li>
      <li>비가 와도 매치가 진행되는 경우 위와 동일</li>
      <li>1회 위반 시 한 달, 2회 위반 시 이용이 영구 정지됩니다.</li>
    </ul>
    <v-divider/>
    <ul>
      <h2>- 취소/환불 -</h2>
      <h4>매치가 취소되는 경우</h4>
      <li>각 구장별 최소 인원 (6 vs 6 구장 10명 / 5 vs 5 구장 8명) 이 되지 않을 경우 경기가 취소 될 수 있으며 진행 여부 안내는 1시간 30분 전까지 카카오톡 알림톡을 통해 안내드리고 있습니다.</li>
      <li>당일 기상악화의 경우에도 환급율은 동일하며 신청 전 꼭 기상 확인 바랍니다.</li>
      <li>인원이 모집되는 경우 우천 시에도 진행되며 참석이 어려울 경우 진행 확정 전 사전 취소 부탁드립니다.</li>
      <li>경기 중 플레이가 어려울 정도로 비가 오는 경우에는 현장에서 판단합니다.</li>
    </ul>
    <ul>
      <h4>신청을 취소할 경우</h4>
      <li>당일 기상악화의 경우에도 환급율은 동일하며 신청 전 꼭 기상 확인 바랍니다.</li>
      <li>인원이 모집되는 경우 우천 시에도 진행됩니다.</li>
      <li>매치시작 1시간 30분 전까지 취소하지 않고 불참하면 향후 이용 제한이 있습니다.</li>
      <li>신청 취소 시 아래 환불 규정에 따라 환불이 진행됩니다.</li>
    </ul>
    <ul>
      <h4>환불 규정</h4>
      <li>경기 2일 전 신청 취소시: 전액 환급</li>
      <li>경기 1일 전 신청 취소시: 참가비의 80% 환금</li>
      <li>경기 당일 3시간 전 신청 취소시: 참가비의 20% 환급</li>
      <li>경기 당일 3시간 미만 신청 취소시: 0%</li>
      <li>인원 부족으로 경기가 취소될 경우에는 최소 1시간 30분 전에 개별적으로 매치 취소 안내를 드리며 해당 캐시는 당일내로 전액 환급 처리됩니다.</li>
      <li>경기 중 부상에 대한 책임은 해당 개인에게 귀속됩니다.</li>
    </ul>
  </v-card>
  <div id="floatdiv">
    <v-btn @click="payment()" style="background-color:blueviolet;" pa-2 x-large block>신 청 하 기</v-btn>
  </div>
</div>
</template>

<script>
import axios from 'axios'
import {store} from '@/store'
import FutMap from './FutMap'
import FutHead from './FutHead'
export default {
  created(){
    if(!store.state.futsal.currentLoc.hasOwnProperty('lng')){
      store.state.futsal.currentLoc = {lng: 126.975598, lat:37.554034}
    }
    if(!store.state.futsal.selectMatch.hasOwnProperty('futsalseq')){
      axios.get(`${this.context}/futsal/match/${this.$route.params.matchId}`)
      .then(res =>{
        store.state.futsal.selectMatch = res.data
        this.selectMatch = res.data
        this.navigation()
      })
    }else{
      this.navigation()
    }
  },
  components:{FutHead,FutMap},
  data(){
    return {
      context:store.state.context,
      height: 30,
      success: 100,
      selectMatch: store.state.futsal.selectMatch,
      fnc: store.state.futsal.fnc,
      difficultyMsg: [
        '초급 매치는 실력에 상관없이 누구나 참여하실 수 있습니다.',
        '일반 매치는 실력에 상관없이 누구나 참여하실 수 있습니다.',
        '상급 매치는 공좀 차는분만 오세요.'
      ],
      mapView: true,
      moveInfo: '',
      temp: {},
      result:{},
      pageurl:'',
    }
  },
  computed: {
    con(){
      return window.console
    },
    moveResult(){
      return this.moveInfo ? 
        `${parseInt(this.moveInfo.properties.totalTime/60)}분 총 거리 : ${
          (this.moveInfo.properties.totalDistance/1000).toFixed(2)
          }Km 택시 예상요금 : ${this.moveInfo.properties.taxiFare} 원`
        : "현재위치가 검색되지 않습니다."
    },
    matchRule(){
      let match = this.selectMatch
      return match.num ?
        [match.num,match.gender,match.difficulty,match.shoes,'minmax']
        : ['1','2','3','4','minmax']
    },
    stadiumFacility(){
      return this.selectMatch.stadiumfacility ?
        this.selectMatch.stadiumfacility.split(',')
        : ['park0','shoes1','shoes0','shower1','wear1']
    },
    stadiumImgChange(){
      return this.selectMatch.stadiumimg
    },
    stadiumImg(){
      return this.stadiumImgChange ?
        this.stadiumImgChange.split(',').map(i => require(`@/assets/img/stadium/${i}.jpg`)) 
        : Array.from({length:3},(_,i) => require(`@/assets/img/stadium/1${i+1}.jpg`))
    },
    stadiumText(){
      let selectMatch = this.selectMatch
      return [
        `${selectMatch.num}vs${selectMatch.num}
        구장의 최소 인원은 ${parseInt(selectMatch.num)*2 -2}명입니다.`,
        `모든 ${selectMatch.num}구장은 정원 모집 시 삼파전으로 진행합니다.`,
        '주차 : 평일 2시간 무료 / 주말 무료',
        '(평일 이용시 주차 차량 번호 기입 필수, 2시간 이상 주차시 추가 비용 발생)',
        '화장실은1층 화장실 이용',
        '자판기 및 흡연 구역 있음'
      ]
    },
  },
  methods: {
    viewTogle(){
      this.mapView=!this.mapView
    },
    msgSwitch(item){
      switch(item){
        case 1 : return '초보자'
        case 2 : return '중급자'
        case 3 : return '상급자'
        case 4 : return '4vs4'
        case 5 : return '5vs5'
        case 6 : return '6vs6'
        case 'shoes1' : return '풋살화 필수'
        case 'shoes0' : return '축구화 가능'
        case 'minmax' : return `${this.selectMatch.num*2 - 2} ~ ${this.selectMatch.num*2 + 4}명`
        case 'male' : return '남성 매치'
        case 'female' : return '여성 매치'
        default : return item
      }
    },
    addressSearch(search,callBack){
      let goalLocation = {lng: 126.975598, lat:37.554034}
      axios({url: 'http://dapi.kakao.com/v2/local/search/keyword.json',//'http://dapi.kakao.com/v2/local/search/address.json',
        headers:{
          Authorization: 'KakaoAK 28d9076d78b899a3f85bb1c12320b0c3'
        },
        method: 'GET',
        params: {
          query: search
        }
      }).then(res=>{
        goalLocation = {lng: res.data.documents[0].x, lat: res.data.documents[0].y}
        callBack(goalLocation)
      }).catch(e=>alert(`adressSearch ${e}`))
    },
    currentLocation(callBack){
      let location = {}
      navigator.geolocation.getCurrentPosition(function(pos) {
      location.lat = pos.coords.latitude
      location.lng = pos.coords.longitude
      callBack(location)
      })
    },
    navigation(){
      this.addressSearch(this.selectMatch.stadiumname,(goalLocation)=>{
        //this.currentLocation((location)=>{
          axios.get(`http://api2.sktelecom.com/tmap/routes`,{
            params: {
              format: 'json',
              version: '2',
              appKey: '5c88a4e4-0f6d-4002-9989-f9e35e5257fe',
              endX: goalLocation.lng,
              endY: goalLocation.lat,
              startX: location.lng ? location.lng : store.state.futsal.currentLoc.lng,
              startY: location.lat ? location.lat : store.state.futsal.currentLoc.lat,
              reqCoordType: 'WGS84GEO',
              resCoordType: 'WGS84GEO',
            }
          }).then(res=>{
            this.moveInfo = res.data.features[0]
          }).catch(e=>alert(`액시오스 실패 ${e}`))
        //})
      })
    },
    payment(){
      if(store.state.person.hasOwnProperty('userid')){
        if((store.state.person.point >= 10000) && (!store.state.person.futblack)){
          axios.post(`${this.context}/res/${this.$route.params.matchId}`
            ,store.state.person)
          .then(res=>{
            if(res.data){
              axios.put(`${this.context}/futsal/match/${this.$route.params.matchId}`)
              .then(()=>{
                alert('결제성공 Match 1 에 예약된 것을 확인하세요')
                window.scrollTo(0,0)
                store.state.person.point = store.state.person.point - 10000
                this.$router.push({name: 'mypage'})
              }).catch(()=>alert('실패'))
            }
          })
          .catch(()=>alert('실패'))
        }else if((store.state.person.point >= 10000) && store.state.person.futblack){
         
          let url = `${this.context}/blackcheck/${store.state.person.userid}`
          let data = {
            userid : store.state.person.userid
          }
          axios
          .post(url,data)
          .then(res => {
            if(res.data.result == 'SUCCESS'){
              alert(`블랙리스트에 등록된 유저입니다.\n블랙 사유 = ${res.data.blackreason} \n${this.$moment(res.data.blacktime).fromNow(true)} 후에 이용가능합니다.`)
            }else{
              store.state.person.futblack = false
              store.state.person.blackreason = ''
              store.state.person.blacktime = res.data.blacktime
              alert('블랙리스트에서 해제되셨습니다. 다시 결제를 시도해주세요')
            }
          })
          /* alert(store.state.person.futblack ? `블랙리스트에 등록된 유저입니다. ${this.$moment(store.state.person.blacktime).fromNow(true)}후에 이용가능합니다.`  : '캐쉬를 충전하세요.') */
          }else if(store.state.person.point < 10000){
            alert('마이페이지에서 캐쉬를 충전하세요')
            this.$router.push({path:'/mypage'})
          }
      }else{alert('로그인 하세요.')}
    }
  }
}
</script>
<style scoped>
.card{
  width: 80%;
  padding: 4px;
  text-align: left;
}
#floatdiv {
  position:fixed;
  display:inline-block;
  width: 100%;
  right:0px; /* 창에서 오른쪽 길이 */
  top:94%; /* 창에서 위에서 부터의 높이 */
  z-index: 100;
}
</style>
