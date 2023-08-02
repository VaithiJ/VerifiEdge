<template>
  <v-container style="width: 100%; ">
    <v-card>
      <v-card-title>SSLC Details</v-card-title>
      <v-card-content>
        <v-container>
          <v-row>
            <v-col style="padding-left: 4%;">
              <h3 class="text-subtitle-1"> Register Number :{{ data.sslc_regno}}</h3>
           <h3 class="text-subtitle-1"> Marks : {{ data.sslc_marks }}</h3>
           <h3 class="text-subtitle-1"> School : {{ data.sslc_school }} </h3>
           <h3 class="text-subtitle-1"> Board : {{ data.sslc_board }}</h3>
           <h3 class="text-subtitle-1"> Year of Completion : {{ data.sslc_passout }}</h3>
           <br>
          <h6 class="text-subtitle-3"> Submitted on : {{ data.submitted_on }}</h6>
              <h6 v-if="data.edited_on" class="text-subtitle-3"> Edited on : {{ data.edited_on }}</h6>
              <h6 v-if="data.approved_on, verified" class="text-subtitle-3"> Approved on : {{ data.approved_on }}</h6>
            </v-col>
            <v-col >
              <v-container v-if="pending" class="text-center">
                <v-icon size="150px" color="yellow" ></v-icon>
              </v-container>
              <v-container v-if="verified" class="text-center">
                <v-icon size="150px" color="green">mdi-check-decagram</v-icon>
              </v-container>
              <v-container v-if="rejected" class="text-center">
                <v-icon size="150px" color="red">mdi-cancel</v-icon>
              </v-container>
              <br>
            </v-col>
          </v-row>
        </v-container>
        <v-row>
          <v-container v-if="!isLoading">
            &emsp;&emsp;
            <v-btn size="30%" v-on:click="verified = true"  :loading="isLoading" :disabled="isLoading" v-if="this.data.status == !'verified' || this.data.status==!'rejected' " text outlined  color="indigo darken-4" style="color:white;"  @click="approve(data.email, data.sslc_regno)">Approve</v-btn>&emsp;
          <v-btn size="30%" v-on:click="rejected = true"  :loading="isLoading" :disabled="isLoading" v-if="this.data.status == !'verified' || this.data.status==!'rejected' " text outlined  color="indigo darken-4" style="color:white;" @click="deny(data.email, data.sslc_regno)">Reject</v-btn>&emsp;
          </v-container>
        </v-row>
        <v-row>
          
          <v-container>
            &emsp;&emsp;
            <v-btn size="30%" text outlined  color="indigo darken-4" style="color: white;" @click="doc(data.email, data.sslc_regno)">Document</v-btn>
          </v-container>
          
        </v-row>
      </v-card-content>
      <v-alert border="top" color="red lighten-1" dismissible  v-if="error"> No Documents uploaded</v-alert>



    </v-card>


       </v-container>
</template>
<script>
import Web3 from "xdc3"
import { ethers } from 'ethers';
import abi from "../app/src/artifacts/contracts/FIlestorage.sol/FileStorage.json"
const contractAddress = '0x51094cD8d5CA57c751328CFDC8b2791D42DB3663';

export default{
   name: 'userprofile',
   props: {
    contract: {
      type: Object,
      required: true,
    },
  },
   async mounted (){
       this.$vuetify.theme.dark =false;
       this.email = this.$storage.getUniversal('user_email')
       let url = "http://127.0.0.1:8000/sslc"
       let res = await this.$axios.get(url,{params:{email: this.email}})
       this.data= res.data
       this.regno = res.data.sslc_regno

       this.notary = this.$storage.getUniversal('notaryemail')
       let nurl = "http://127.0.0.1:8000/notary"
        let nres = await this.$axios.get(nurl,{params:{email: this.notary}})
        this.ndata = nres.data
        if (this.data.status == "pending"){
          this.pending = true
          this.verified = false
          this.rejected = false
        }
        if(this.data.status == "verified"){
          this.verified = true
          this.pending = false
          this.rejected = false
        }
        if(this.data.status == "rejected"){
          this.rejected = true
          this.pending = false
          this.verified = false
        }



   },
   data: () =>({
       email:"",
       data:{},
       regno:"",
       pending: false,
       error:false,
       verified: false,
       rejected: false,
       isLoading:false,
       
   }),
   methods:{
    async doc(email, sslc_regno){
      let url = "http://127.0.0.1:8000/download/S3files"

      this.$axios.get(url,{
        params:{
          email: email,
          regno: this.regno
        },
        responseType: 'arraybuffer'
      })
      .then(response => {
        console.log(response)
        if(response.data == false){
          this.error == true
        }
        else{
          this.error = false
          let blob = new Blob([response.data], { type: 'application/pdf'}),
        url = window.URL.createObjectURL(blob)

        window.open(url)
        }
      })
      console.log(sslc_regno)
    },

    async approve(email, sslc_regno){
      let nurl = "http://127.0.0.1:8000/sslc/inprogress"
      let data={
        'email':this.email,
      }
      let nres= await this.$axios.post(nurl,data)

      let hurl = "http://127.0.0.1:8000/Hash/S3files"

      let hres = await this.$axios.get(hurl,{params:{email: email, regno: this.regno}})
      this.hash = hres.data
      console.log(this.hash)

      try {
      if (!this.contract) {
        console.error('Contract not initialized yet. Please connect with MetaMask first.');
        return;
      }

      const regNo = this.regno // Replace with the registration number
      const fileHash = this.hash// Replace with the file hash
      const userEmail = this.email

      // Call the storeFile function in the contract
      await this.contract.storeFile(regNo, userEmail, fileHash);
      const status = 'File stored successfully!'
      console.log('File stored successfully!');
      
      this.render = false;
      if (status == 'File stored successfully!'){
      let url = "http://127.0.0.1:8000/verify/sslc"
      let vdata={
        user_email: email,
        sslc_regno: sslc_regno,
        notary_email: this.ndata.email,
        notary_name: this.ndata.name
      }
      let res = await this.$axios.post(url, vdata)
      console.log(res.data)
      this.isLoading = true;

    } 

    } catch (error) {
      console.error('Error storing file:', error);
    }
  

     },
    async deny(email, sslc_regno){
        console.log(email)
        let url = "http://127.0.0.1:8000/verify/sslc"
        let reject={
          user_email: email,
          sslc_regno: sslc_regno,
          notary_email: this.ndata.email,
          notary_name: this.ndata.name,
          status: "rejected"
        }
        let res = this.$axios.post(url,reject)
        this.isLoading = true;

    }
   }
}
</script>
