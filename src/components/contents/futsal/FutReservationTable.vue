<template>
<div class="ma-auto">
	<!-- 부트 스트랩 
		<div v-for="(match,index) of matchFilter(propstadium,proptime)" :key="index"
	style="display: grid; grid-template-columns: 10% 20% 45% 25%; border: solid 1px;">
		<div v-for="(item,key) of match" :key="key" @click="click(item,key)" style="text-align: center">
			{{key!='time' ? item : timeToDate(item)}}
		</div>
	</div> -->
	<h3 style="color:white;">{{fnc.timeToDate(propTime)}}  {{propStadium}}</h3>
	<v-card raised class="ma-auto">
		<v-data-table
		:headers="headers" :items="table"
		:items-per-page="1000"
		class="elevation-1 ma-2" hide-default-footer>
			<template v-slot:item.time="{item}">
				<h5 style="margin-top:10px">{{new Date(item.time).getHours()}}:00</h5>
				<!-- <v-btn @click="click(item)">{{timeToDate(item.time)}}</v-btn> -->
			</template>
			<template v-slot:item.stadiumname="{item}">
				<h6 style="margin-top:10px">{{item.stadiumname}}</h6>
				<!-- <v-btn @click="click(item)">{{timeToDate(item.time)}}</v-btn> -->
			</template>
			<template v-slot:item.num="{item}">
				<h6 style="margin-top:10px">{{`${item.num}vs${item.num}`}}</h6> 
			</template>
			<template v-slot:item.gender="{item}">
				<v-img height="30" width="30" class="pa-2" style="margin-top:10px"
				:src="require(`@/assets/img/matchRule/${item.gender}.svg`)"/>
			</template>
			<template v-slot:item.difficulty="{item}">
				<v-img class="d-inline-flex" style="margin-top:10px"
					width="30px" height="30px"
					v-for="n of parseInt(item.difficulty)" 
					:key="n" src="@/assets/img/matchRule/star.png">
				</v-img>
				
			</template>
			<template v-slot:item.remain="{item}">
				<v-btn style="margin-top:5px" @click="selectMatch(item)" :color="item.remain > 3 ? '#2478FF' : 
					item.remain!=0 ? '#FF3636' : '#8C8C8C'">
					{{item.remain > 3 ? `${item.remain}자리 남음` : item.remain!=0 ? '마감임박' : '예약마감'}}
				</v-btn>
			</template>
		</v-data-table>
	</v-card>
</div>
</template>
<script>
import {store} from '@/store'
export default{
	props: [
		'propTime',
		'propStadium',
		'propTable'
	],
	data(){
		return{
			headers: [
				{ text: '경기날짜', width: 100, value: 'time' },
				{ text: '경기장', width: 250, value: 'stadiumname' },
				{ text: '인원', value: 'num' },
				{ text: '성별', value: 'gender'},
				{ text: '난이도', value: 'difficulty' },
				{ text: '예약가능 인원', width: 200, align: 'center',sortable: false,value: 'remain'}
			],
			fnc: store.state.futsal.fnc,
		}
	},
	computed: {
		con(){
			return window.console
		},
		table(){
			return this.propTable
		}
	},
	methods:{
		selectMatch(param){
			if(param.remain != 0){
				store.state.futsal.selectMatch = param
				this.$router.push({ name: 'futsalmatch', params: { matchId: param.futsalseq }})
			}
		}
	}
}
</script>
<style scoped>
td:not(td){
	vertical-align: middle;
}
</style>