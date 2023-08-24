<template>


  <v-container style="width: 100%; ">
    <v-card>
      <v-card-title>Personal Details</v-card-title>
      <v-card-content>
        <v-container>
          <v-row>
            <v-col style="padding-left: 4%;">
              <table style="width: 100%; border-collapse: collapse; border: 1px solid #ccc;">
                <tr style="border-bottom: 1px solid #ccc;">
                  <td style="padding: 10px;"><h4 class="text-subtitle-3">Name :</h4></td>
                  <td style="padding: 10px;"><h5 class="text-subtitle-3">{{ pdata.name }}</h5></td>
                </tr><tr style="border-bottom: 1px solid #ccc;">
                  <td style="padding: 10px;"><h4 class="text-subtitle-3">Email ID :</h4></td>
                  <td style="padding: 10px;"><h5 class="text-subtitle-3">{{ pdata.email }}</h5></td>
                </tr>
                <tr style="border-bottom: 1px solid #ccc;">
                  <td style="padding: 10px;"><h4 class="text-subtitle-3">Mobile Number:</h4></td>
                  <td style="padding: 10px;"><h5 class="text-subtitle-3">{{ pdata.mob }}</h5></td>
                </tr>
                <tr style="border-bottom: 1px solid #ccc;">
                  <td style="padding: 10px;"><h4 class="text-subtitle-3">Aadhaar Number:</h4></td>
                  <td style="padding: 10px;"><h5 class="text-subtitle-3">{{ pdata.aadhaar }}</h5></td>
                </tr>
                <tr style="border-bottom: 1px solid #ccc;">
                  <td style="padding: 10px;"><h4 class="text-subtitle-3">Company Name :</h4></td>
                  <td style="padding: 10px;"><h5 class="text-subtitle-3">{{ pdata.company_name }}</h5></td>
                </tr>
         
              </table>
              <br>
          <h6 class="text-subtitle-3"> Submitted on : {{ pdata.submitted_on }}</h6>
          <h6 v-if="pdata.approved_on, verified" class="text-subtitle-3"> Approved on : {{ pdata.approved_on }}</h6>

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

            </v-col>
            <v-container>

            </v-container>

          </v-row>
          <v-row>
            <v-container>
              &emsp;&emsp;
              <v-btn size="30%" text outlined  color="blue lighten-1" style="color: white;" @click="doc(pdata.email, pdata.aadhaar)">Document</v-btn>
            </v-container>
          </v-row>
        </v-container>
      </v-card-content>



    </v-card>

  </v-container>
</template>

<script>
import { ethers } from 'ethers';
import abi from "../app/src/artifacts/contracts/FIlestorage.sol/FileStorage.json"
const contractAddress = '0x51094cD8d5CA57c751328CFDC8b2791D42DB3663'; 
import Web3 from "xdc3"
export default{
name: 'userprofile',
async mounted (){
  this.$vuetify.theme.dark =false;
  this.email = this.$storage.getUniversal('search_email')
  let url = "http://127.0.0.1:8000/personal"
  let res = await this.$axios.get(url,{params:{ email :this.email}});
  this.pdata=res.data

  if (this.pdata.status == "pending"){
    this.pending = true
    this.verified = false
    this.rejected = false
  }
  if(this.pdata.status == "verified"){
    this.verified = true
    this.pending = false
    this.rejected = false
  }
  if(this.pdata.status == "rejected"){
    this.rejected = true
    this.pending = false
    this.verified = false
  }

  this.notary = this.$storage.getUniversal('notaryemail')
  let nurl = "http://127.0.0.1:8000/notary"
  let nres = await this.$axios.get(nurl,{params:{email: this.notary}})
  this.ndata = nres.data
  console.log(this.pdata)

},
data: () =>({
  email:"",
  pdata :{},
  ndata:{},
  pending: false,
  verified: false,
  rejected: false
}),
methods:{
  async doc(email, aadhaar){

let hurl = "http://127.0.0.1:8000/Hash/S3files"
let hres = await this.$axios.get(hurl,{params:{email: email, regno: aadhaar}})
this.hash = hres.data
console.log(this.hash)

const provider = new ethers.providers.Web3Provider(window.ethereum);
const web3 = window.web3;
const signer = provider.getSigner();

this.contract = new ethers.Contract(contractAddress, abi.abi, signer);
console.log(this.contract)
try {
if (!this.contract) {
  console.error('Contract not initialized yet. Please connect with MetaMask first.');
  return;
}

const regNo = aadhaar // Replace with the registration number
const userEmail = this.email

// Call the storeFile function in the contract
this.get = await this.contract.getFile(regNo, userEmail);
this.blockhash = this.get[2]
console.log(this.blockhash);
} catch (error) {
console.error('Error getting file:', error);
}

if(this.blockhash == this.hash){
let url = "http://127.0.0.1:8000/download/S3files"

this.$axios.get(url,{
  params:{
    email: email,
    regno: aadhaar
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
console.log(aadhaar)
} 
else{
console.log('error')
}
},
}
}
</script>
