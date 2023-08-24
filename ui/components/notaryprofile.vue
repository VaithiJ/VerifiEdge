<template>


    <v-container style="width: 100%; ">
      <v-card class="curved-box" elevation="12">

      <v-card-title>Notary Registered Details</v-card-title>
          <v-container>
            <v-row>
              <v-col >
                <table style="width: 90%; border-collapse: collapse; border: 1px solid #ccc;">
                
                  <tr style="border-bottom: 1px solid #ccc;">
                    <td style="padding: 10px;"><h4 class="text-subtitle-3">Full Name:</h4></td>
                    <td style="padding: 10px;"><h5 class="text-subtitle-3">{{ pdata.name }}</h5></td>
                  </tr>
                  <tr style="border-bottom: 1px solid #ccc;">
                    <td style="padding: 10px;"><h4 class="text-subtitle-3">Email:</h4></td>
                    <td style="padding: 10px;"><h5 class="text-subtitle-3">{{ pdata.email }}</h5></td>
                  </tr>
                  <tr style="border-bottom: 1px solid #ccc;">
                    <td style="padding: 10px;"><h4 class="text-subtitle-3">Mobile Number : </h4></td>
                    <td style="padding: 10px;"><h5 class="text-subtitle-3">{{ pdata.mob }}</h5></td>
                  </tr>
                  <tr style="border-bottom: 1px solid #ccc;">
                    <td style="padding: 10px;"><h4 class="text-subtitle-3">Aadhaar Number :</h4></td>
                    <td style="padding: 10px;"><h5 class="text-subtitle-3">{{ pdata.aadhaar }}</h5></td>
                  </tr>
                  <tr style="border-bottom: 1px solid #ccc;">
                    <td style="padding: 10px;"><h4 class="text-subtitle-3">PAN Number :</h4></td>
                    <td style="padding: 10px;"><h5 class="text-subtitle-3">{{ pdata.pan }}</h5></td>
                  </tr>
                  <tr style="border-bottom: 1px solid #ccc;">
                    <td style="padding: 10px;"><h4 class="text-subtitle-3">Year of Completion :</h4></td>
                    <td style="padding: 10px;"><h5 class="text-subtitle-3">{{ pdata.address }}</h5></td>
                  </tr>
                  <tr style="border-bottom: 1px solid #ccc;">
                    <td style="padding: 10px;"><h4 class="text-subtitle-3">City :</h4></td>
                    <td style="padding: 10px;"><h5 class="text-subtitle-3">{{ pdata.city }}</h5></td>
                  </tr>
                  <tr style="border-bottom: 1px solid #ccc;">
                    <td style="padding: 10px;"><h4 class="text-subtitle-3">Pincode :</h4></td>
                    <td style="padding: 10px;"><h5 class="text-subtitle-3">{{ pdata.pincode }}</h5></td>
                  </tr>
                  <tr style="border-bottom: 1px solid #ccc;">
                    <td style="padding: 10px;"><h4 class="text-subtitle-3">State :</h4></td>
                    <td style="padding: 10px;"><h5 class="text-subtitle-3">{{ pdata.state }}</h5></td>
                  </tr>
                  <tr style="border-bottom: 1px solid #ccc;">
                    <td style="padding: 10px;"><h4 class="text-subtitle-3">Country :</h4></td>
                    <td style="padding: 10px;"><h5 class="text-subtitle-3">{{ pdata.country }}</h5></td>
                  </tr>
                  
                </table>

              <br>                <br>
              <v-row>
                <v-spacer></v-spacer>

                <v-col>
                  <v-btn size="30%" color="blue lighten-1" style="color:white" @click="aadhaar(pdata.email, pdata.aadhaar)">Aadhaar Card</v-btn>
                </v-col>
&ensp;&ensp;
                <v-col>
                  <v-btn size="30%" color="blue lighten-1" style="color:white" @click="pan(pdata.email, pdata.pan)">Pan Card</v-btn>
                </v-col>
                
                <v-col>
                  <v-btn size="30%" color="blue lighten-1" style="color:white" @click="education(pdata.email, pdata.mob)">Certificate</v-btn>
                </v-col>
                <v-spacer></v-spacer>                <v-spacer></v-spacer>

              </v-row>

             </v-col>
             

                  
              </v-row>

          </v-container>
        </v-card>

          </v-container>



  </template>

  <script>
  export default{
  name: 'notaryprofile',
  
  async mounted (){
    this.$vuetify.theme.dark =false;
    this.email = this.$storage.getUniversal('notary_email')
    let url = "http://127.0.0.1:8000/notary"
    let res = await this.$axios.get(url,{params:{ email :this.email}});
    this.pdata=res.data
    console.log(this.pdata)

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

        this.admin_email = this.$storage.getUniversal('admin_mail')
        let nurl = "http://127.0.0.1:8000/admin"
        let nres = await this.$axios.get(nurl,{params:{admin_email: this.admin_email}})
        this.ndata = nres.data
        console.log(this.ndata)



  },
  data: () =>({
    email:"",
    admin_email:"",
    pdata :{},
    ndata:{},
    error: false,
    success: false,
    pending: false,
    verified: false,
    rejected: false,
    isLoading:false,



  }),
  methods:{
    async aadhaar(email, aadhaar){
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
    async pan(email, pan){
      this.$axios.get("http://127.0.0.1:8000/download/S3files",{
        params:{
          email: email,
          regno: pan
        },
        responseType: 'arraybuffer'
      })
      .then(response => {
        console.log(response)

        let blob = new Blob([response.data], { type: 'application/pdf'}),
        url = window.URL.createObjectURL(blob)

        window.open(url)
      })
      console.log(pan)

    },
    async education(email, mob){
      this.$axios.get("http://127.0.0.1:8000/download/S3files",{
        params:{
          email: email,
          regno: mob
        },
        responseType: 'arraybuffer'
      })
      .then(response => {
        console.log(response)

        let blob = new Blob([response.data], { type: 'application/pdf'}),
        url = window.URL.createObjectURL(blob)

        window.open(url)
      })
      console.log(mob)

    },
    async approve(email,name){
      let url="http://127.0.0.1:8000/notary/verification"
      let verify={
        email:email,
        admin_email:this.ndata.admin_email,
        name:name
      }
    let res= await this.$axios.post(url,verify)
    window.location.reload()
  },
    async deny(email,name){
    console.log(email,name)
    let url ="http://127.0.0.1:8000/notary/verification"
    let reject={
      email:email,
      admin_email:this.ndata.admin_email,
      name:name,
      status:"rejected"
    }
    let res= await this.$axios.post(url,reject)
    this.isLoading = true;

    window.location.reload()
  }
  }
  }
  </script>
<style>
.curved-box {
    border-radius: 40px;
    box-shadow: 0px 0px 20px rgba(0, 0, 0, 0.1);
  }
</style>