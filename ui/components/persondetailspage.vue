<template>
  <v-container style="width: 100%; ">
    <v-card class="curved-box" elevation="12">
      <v-container v-if="data_">
        <div class="custom-alert">
            <v-icon class="alert-icon">mdi-alert-circle</v-icon>
            <span>Personal Data is mandatory.</span>
          </div>
      </v-container>
      <v-card-title>Personal Details</v-card-title>
      <v-card-content>
        <v-container v-if="data_s">
          <v-row>
            <v-col style="padding-left: 4%;">
              <table style="width: 100%; border-collapse: collapse; border: 1px solid #ccc;">
                
                <tr style="border-bottom: 1px solid #ccc;">
                  <td style="padding: 10px;"><h4 class="text-subtitle-3">Mobile Number:</h4></td>
                  <td style="padding: 10px;"><h5 class="text-subtitle-3">{{ pdata.mob }}</h5></td>
                </tr>
                <tr style="border-bottom: 1px solid #ccc;">
                  <td style="padding: 10px;"><h4 class="text-subtitle-3">Date of Birth :</h4></td>
                  <td style="padding: 10px;"><h5 class="text-subtitle-3">{{ pdata.dob }}</h5></td>
                </tr>
                <tr style="border-bottom: 1px solid #ccc;">
                  <td style="padding: 10px;"><h4 class="text-subtitle-3">Aadhar Number:</h4></td>
                  <td style="padding: 10px;"><h5 class="text-subtitle-3">{{ pdata.aadhaar }}</h5></td>
                </tr>
                <tr style="border-bottom: 1px solid #ccc;">
                  <td style="padding: 10px;"><h4 class="text-subtitle-3">Company Name :</h4></td>
                  <td style="padding: 10px;"><h5 class="text-subtitle-3">{{ pdata.company_name }}</h5></td>
                </tr>
                <tr style="border-bottom: 1px solid #ccc;">
                  <td style="padding: 10px;"><h4 class="text-subtitle-3">Company Email:</h4></td>
                  <td style="padding: 10px;"><h5 class="text-subtitle-3">{{ pdata.company_mail }}</h5></td>
                </tr>
              </table>
              <br>
              <h6 class="text-subtitle-3"> Submitted on : {{ pdata.submitted_on }}</h6>
              <h6 v-if="pdata.edited_on" class="text-subtitle-3"> Edited on : {{ pdata.edited_on }}</h6>
              <h6 v-if="pdata.approved_on, verified" class="text-subtitle-3"> Approved on : {{ pdata.approved_on }}</h6>

            </v-col>
            <v-col >
              <v-container v-if="pending" class="text-center">
              </v-container>
              <v-container v-if="pdata.status == 'verified'" class="text-center">
                <v-icon size="150px" color="green">mdi-check-decagram</v-icon>
              </v-container>
              <v-container v-if="pdata.status == 'rejected'" class="text-center">
                <v-icon size="150px" color="red">mdi-cancel</v-icon>
                <br>
                <v-btn color="blue lighten-1" style="color: white;" @click="edit()">EDIT</v-btn>
              </v-container>

            </v-col>


          </v-row>
          <v-row>
            <v-container  v-if="this.datapdf == true || show">
              &emsp;&emsp;
  
              <v-btn size="30%" text outlined color="blue lighten-1" style="color: white;" @click="doc(pdata.email, pdata.aadhaar)">Aadhaar Card</v-btn>
  
            </v-container>
          </v-row>
          <v-row>
            <v-col v-if="this.datapdf == false  &&!isLoading">
              &emsp;&emsp;

              <v-btn size="30%"   :loading="isLoading" :disabled="isLoading"  text outlined color="blue lighten-1" style="color:white;"  @click="showForm = true">Upload Aadhaar Card</v-btn>
              <v-dialog v-model="showForm" max-width="500px">
                <v-card>
                  <v-card-title>
                    <span class="headline">Reason</span>
                  </v-card-title>
          
                  <v-card-text>
                    <v-form ref="form" v-model="valid">
                    <v-row>
                      <v-file-input  style="width:60%;" @change="fileselect"  label = "Upload sslc doc" ></v-file-input>
    
                    </v-row>
                    </v-form>
                  </v-card-text>
          
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="error" text @click="showForm = false">Cancel</v-btn>
                    <v-btn text color="blue lighten-1"  @click="upload(pdata.email, pdata.aadhaar)"  class="button">Submit</v-btn>
                  </v-card-actions>
        
                </v-card>
              </v-dialog>      
            </v-col>
            
          </v-row>
        </v-container>
      </v-card-content>
      <v-card-action >
        <v-container v-if="data_">
          <v-btn text icon @click="addpersonal()"><v-icon color="blue lighten-1">mdi-plus</v-icon></v-btn>

        </v-container>

      </v-card-action>


    </v-card>

  </v-container>
</template>

<script>
export default{
name: 'userprofile',
async mounted (){
  this.$vuetify.theme.dark =false;
  this.email = this.$storage.getUniversal('login_mail')
  let url = "http://127.0.0.1:8000/personal"
  let res = await this.$axios.get(url,{params:{ email :this.email}});
  this.pdata=res.data
  this.regno = res.data.aadhaar
  console.log(this.pdata)

  let nurl = "http://127.0.0.1:8000/check-s3-folder"
  let nres = await this.$axios.get(nurl,{params:{email: this.email, regno: this.regno}})
  this.datapdf = nres.data.file_present
  console.log(nres.data)
  if(this.datapdf == true){
    this.show = true

  }

  if(this.pdata == false){
        this.data_ = true,
        this.data_s = false
      }
      else{
        this.data_s = true,
        this.data_ = false
      }
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


},
data: () =>({
  email:"",
  pdata :{},
  pending: false,
  verified: false,
  rejected: false,
  data_: false,
  datapdf:"",
  isLoading:false,
  data_s: false,
  show: false,
  showForm: false,


}),
methods:{
  async fileselect(event){
      this.file=event
    },
    async upload(email, aadhaar){
      

            let formdata= new FormData()
            formdata.append('email', email)
            formdata.append('regno', aadhaar)
            formdata.append('file',this.file)
            let furl = "http://127.0.0.1:8000/uploadfile/S3"
            let res = await this.$axios.post(furl,formdata,{ headers : {'Content-Type': 'application/json',}});
            if(res.data == true){
              this.isLoading = true;
              let nurl = "http://127.0.0.1:8000/user/expupdation"
              let ndata={
                'email': this.email
              }
              let nres = await this.$axios.post(nurl, ndata)
              window.location.reload()
            }
            // Simulate an asynchronous operation, such as an API call
   },

    async doc(email, aadhaar){
      this.$axios.get("http://127.0.0.1:8000/download/S3files",{
        params:{
          email: email,
          regno: aadhaar
        },
        responseType: 'arraybuffer'
      })
      .then(response => {
        console.log(response)

        let blob = new Blob([response.data], { type: 'application/pdf'}),
        url = window.URL.createObjectURL(blob)

        window.open(url)
      })
      console.log(aadhaar)

    },
  async edit(){
    this.$router.push("/personal_edit")
  },
  async addpersonal(){
    this.$router.push('/onboard')
  }
}
}
</script>
<style>
.curved-box {
    border-radius: 40px;
    box-shadow: 0px 0px 20px rgba(0, 0, 0, 0.1);
  }
.custom-alert {
  display: flex;
  align-items: center;
  background-color: transparent;
  color: #008cff; 
}

.custom-alert .alert-icon {
  color: #008cff; 
  margin-right: 5px;
}

.custom-alert .alert-text {
  font-size: 16px;
}

</style>