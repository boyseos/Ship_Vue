<template>
<div>
    <!-- :propLocation="location" -->
  <fut-map v-if="mapTogle" :style="`height: ${height[0]}vh; width:100%;`"
    :propSearchWord="`${stadiumName} 풋살 경기장`"
    :propLocation="location" :propRightClick="true"
    :propRoadView="true"
    @sendStadiumName="setStadium"></fut-map>
  <fut-head v-else :style="`height: ${height[0]}vh`"
    :propImg="headImg" class="table"></fut-head>
  <fut-search-bar :style="`height: ${height[1]}vh`" class="table"
    @sendStadiumName="setStadium" @sendLocation="setGps"></fut-search-bar>
  <fut-reservation :style="`height: ${height[2]}vh`" class="table"
    @sendTime="setTime"></fut-reservation>
  <fut-reservation-table ma-auto class="table"  
    :propTime="time" :propStadium="stadiumName" :propTable="fnc.matchFilter(time,stadiumName,table)"></fut-reservation-table>

</div>
</template>

<script>
import FutMap from './futsal/FutMap'
import FutHead from './futsal/FutHead'
import FutSearchBar from './futsal/FutSearchBar'
import FutReservation from './futsal/FutReservation'
import FutReservationTable from './futsal/FutReservationTable'
import {store} from '@/store'
import axios from 'axios'
export default {
  components:{FutHead,FutSearchBar,FutReservation,FutReservationTable,FutMap},
  data(){
    return{
      context:store.state.context,
      fnc: store.state.futsal.fnc,
      headImg: [
        'https://blog.hmgjournal.com/images/contents/article/20161214-Reissue-night-football-03.jpg',
        'http://641109.igkorea.net/data/editor/1803/7ec6014758f9af0fd497c55e30ef7fd1_1522042126_13.jpg',
        'http://641109.igkorea.net/data/editor/1803/7ec6014758f9af0fd497c55e30ef7fd1_1522042791_19.jpg'
      ],
      stadiumName : '',
      location: '',
      mapTogle: false,
      time : Date.now(),
      table : [],
      height:[50,5,7],
      // msgList: ["무엇을 도와드릴까요?"],
      // msg: "",
      // console: ""
    }
  },
  created(){
    let table = []
    axios.get(`${this.context}/futsal/`)
      .then(res => {
        table = res.data
    }).catch(e => {
      alert(`axios fail ${e} 랜덤데이터 대체`)
      const ranAddr = () => '어디어디 어디 주소 어디어디 어디 길'
      const ranTel = () => `010-${[parseInt(Math.random()*9999)]}-${[parseInt(Math.random()*9999)]}`
      const ranName = () => ['신촌','부산','용산','인천','서울','영등포'][parseInt(Math.random()*6)]
      const rannum = () => [4,5,6][parseInt(Math.random()*3)]
      const rangender = () => ['female','male'][parseInt(Math.random()*2)]
      const ranrating = () => parseInt(Math.random()*3+1)
      const rantime = x => x + Math.random()*1000*3600*24*13
      const ranfacility = () => 'size0,shower0,park0,shoes0,wear0'
      const remain = () => parseInt(Math.random()*12)
      table = Array.from({length : 200},(_,i) => ({
        futsalseq: i,
        time: rantime(Date.now()), stadiumname: ranName(),
        stadiumaddr: ranAddr(), stadiumtel: ranTel(),
        num : rannum(), gender: rangender(),difficulty: ranrating(),
        shoes: 'shoes0', stadiumfacility: ranfacility(),
        stadiumimg: '11,12,13', remain: remain(), adminname: '펭수'
      }))
    }).finally(()=>{
      table.map(x =>{
        x.stadiumGroundSize = x.stadiumfacility.split(',')[0]
        x.stadiumShower = x.stadiumfacility.split(',')[1]
        x.stadiumParking = x.stadiumfacility.split(',')[2]
        x.stadiumShoesRental = x.stadiumfacility.split(',')[3]
        x.stadiumDressRental = x.stadiumfacility.split(',')[4]
      })
      this.table = table
      store.state.futsal.matchList = table	
    })
	},
  computed: {
  },
  methods: {
    setTime(time){
      this.time = time
    },
    setStadium(stadiumName){
      this.stadiumName = stadiumName.place_name ? stadiumName.place_name : stadiumName
      this.mapTogle = this.stadiumName == '' ? false : true
    },
    setGps(location){
      this.location = {lat: location.lat,lng: location.lng}
      this.mapTogle = true
    },
    // botChat(){
    //   axios({url: `${store.state.context}/bot/${this.msg}`, method: 'GET'})
    //   .then(res=>{
    //     this.msgList.push(this.msg)
    //     this.msg = ''
    //     this.console = res.data
    //     if(res.data.msg.includes('예약')){
    //       let time = res.data.result.time
    //       let year = time.match(/\d{1,4}년/)
    //       let month = time.match(/\d{1,2}월/)
    //       let day = time.match(/\d{1,2}일/)
    //       let hour = time.match(/\d{1,2}시/)
    //       this.location = res.data.result.location
    //       this.mapTogle = true
    //       let x = z => z.substring(0,z.length-1)
    //       time = Date.parse(`${year ? x(year[0]) : new Date().getFullYear()}-${month ? x(month[0]) : new Date().getMonth()+1}-${day ? x(day[0]) : new Date().getDate()} ${hour ? x(hour[0]) : '00'}:00`)
    //     }
    //   })
    // },
  }
}
</script>
<style scoped>
.table{
	padding: 3px;
}

</style>
