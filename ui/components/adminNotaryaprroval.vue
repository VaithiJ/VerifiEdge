<template>


    <v-container style="width: 100%; ">

          <v-container>
            <v-row>
                <v-container v-if="pending" class="text-center">
                    <v-icon size="150px" color="yellow" ></v-icon>
                  </v-container>
                  <v-container v-if="verified" class="text-center">
                    <v-icon size="150px" color="green">mdi-check-decagram</v-icon>
                  </v-container>
                  <v-container v-if="rejected" class="text-center">
                    <v-icon size="150px" color="red">mdi-cancel</v-icon>

                  </v-container>
            </v-row>
            <v-row>
              <v-col v-if="!isLoading">
                <v-container>
                  &emsp;&emsp;

                  <v-btn size="30%"   :loading="isLoading" :disabled="isLoading" v-if="this.pdata.status == !'verified' || this.pdata.status==!'rejected'" color="blue lighten-1" style="color:white;" @click="approve(pdata.email, ndata.name)">Approve</v-btn>&emsp;


                </v-container>
             </v-col>
             <v-col v-if="!isLoading">
                <br>              
                  <v-container>
  
  
                    <v-btn size="30%"   :loading="isLoading" :disabled="isLoading" v-if="this.pdata.status == !'verified' || this.pdata.status==!'rejected'" color="blue lighten-1" style="color:white;" @click="deny(pdata.email, ndata.name)">Reject</v-btn>
  
                  </v-container>
                  
               </v-col>
             
       
                  
              </v-row>

          </v-container>

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
  },

  }
  }
  </script>
<style>
.curved-box {
    border-radius: 40px;
    box-shadow: 0px 0px 20px rgba(0, 0, 0, 0.1);
  }
</style>