<template>
<div id="app" >
  <v-btn color="blue darken-2" dark fixed center @click="dialog = !dialog"  style="font-size:15px" > 로그인 </v-btn>
    <v-dialog v-model="dialog" width="400px" >
      <v-card >
        <v-card-title class="indigo " font-color="white" > 로그인 </v-card-title>
        <v-container fluid  >
          <v-layout wrap  >
            <v-flex xs8  >
              <v-text-field center prepend-icon="people" v-model="userid" label="ID" required></v-text-field>
              <v-text-field prepend-icon="lock" label="PASSWORD" type="password" v-model="passwd"></v-text-field>
              <v-checkbox v-model="checkbox" label="로그인 유지">
              </v-checkbox>
              
            </v-flex>
            
          </v-layout>
        </v-container>

      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn text color="primary" @click="login()">LOGIN</v-btn>
        <v-btn text color="red" @click="dialog = false">CANCEL</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</div>

</template>
<script>
import axios from 'axios'
import {store} from '../../store'
export default {
  components:{
  },
   data(){
      return {
        checkbox: false,
        dialog: false,
        result : '',
        userid : '',
        passwd : '',
        person : '',
        context: store.state.context,
        state : store.state,
        icons: [
                'mdi-facebook-box',
                'mdi-google-plus',
                'mdi-instagram',
        ]
      }
    },
   methods:{
      login(){
        let url = `${this.context}/login`
        let data =  {
         userid : this.userid,
         passwd : this.passwd
        }
        let headers= {
               'authorization': 'JWT fefege..',
               'Accept' : 'application/json',
               'Content-Type': 'application/json'
        }
      axios
      .post(url, data,headers)
      .then(res=>{
            if(res.data.result === "SUCCESS"){
                store.state.person = res.data.person
                this.state.authCheck = true
                if(parseInt(this.$moment(this.state.person.blacktime).format('x')) < parseInt(Date.now())){
                  this.deleteBlack()
                }
                window.sessionStorage.setItem('person',JSON.stringify(store.state.person))
                if(this.checkbox){
                  window.localStorage.setItem('person',JSON.stringify(store.state.person))
                }
                this.dialog=false
            }else{
                alert(`로그인 실패`)
                this.$router.go({path: '/login'})
            }
         this.result = res.data
      })
      .catch(e=>{
         alert('로그인 실패 error code=>'+e)
        })
      },
      deleteBlack(){
      let url = `${this.context}/deleteBlack/${this.userid}`
      let data = {
        userid : this.userid
      }
      axios
      .put(url,data)

    }
   }
}
</script>
<style scoped>
.layout {
  justify-content:center;
}
.theme--light.v-card{
  color:white;
}

</style>