<template>


    <v-container style="width: 80%; ">
  
          <v-container>
            <v-row>
              <v-col cols="12">
                <h3 class="text-title-1">Company Name</h3>
                <h3 class="text-subtitle-1">{{ pdata.name }}</h3>
                <h3 class="text-title-1">Comapny Reg.No</h3>
                <h3 class="text-subtitle-1">{{ pdata.company_reg }}</h3>
                <h3 class="text-title-1">Company Mail</h3>
                <h3 class="text-subtitle-1">{{ pdata.company_mail }}</h3>
                <h3 class="text-title-1">Business Type</h3>
                <h3 class="text-subtitle-1">{{ pdata.business_type }}</h3>
                <h3 class="text-title-1">Industry</h3>
                <h3 class="text-subtitle-1">{{ pdata.industry }}</h3>
                <h3 class="text-title-1">Address </h3>
                <h3 class="text-subtitle-1">{{ pdata.address }}</h3>
                <h3 class="text-title-1">Mobile Number</h3>
                <h3 class="text-subtitle-1">{{ pdata.mob }}</h3>

               <br>
                <!---<h6 class="text-subtitle-3"> Submitted on : {{ pdata.submitted_on }}</h6>
                <h6 v-if="pdata.edited_on" class="text-subtitle-3"> Edited on : {{ pdata.edited_on }}</h6>
                <h6 v-if="pdata.approved_on" class="text-subtitle-3"> Approved on : {{ pdata.approved_on }}</h6>--->
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
              </v-row>
          </v-container>
          <v-row>
                <v-container v-if="!isLoading">
                  &emsp;&emsp;
                  <v-btn size="30%"   :loading="isLoading" :disabled="isLoading" v-if="this.pdata.status == !'verified' || this.pdata.status==!'rejected'" color="blue lighten-1" style="color:white;" @click="approve(pdata.company_mail, ndata.name)">Approve</v-btn>&emsp;
                  <v-btn size="30%"   :loading="isLoading" :disabled="isLoading" v-if="this.pdata.status == !'verified' || this.pdata.status==!'rejected'"  color="blue lighten-1" style="color:white;" @click="deny(pdata.company_mail, ndata.name)">Reject</v-btn>
                </v-container>
              </v-row>
              <br>

    </v-container>
  </template>
  
  <script>
  export default{
  name: 'companyprofile',
  
    
  async mounted (){
    this.$vuetify.theme.dark =false;
    this.company_mail = this.$storage.getUniversal('company_email')
    let url = "http://127.0.0.1:8000/hr"
    let res = await this.$axios.get(url,{params:{ company_mail :this.company_mail}});
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
    company_mail:"",
    admin_email:"",
    pdata :{},
    ndata:{},
    error: false,
    success: false,
    pending: false,
    verified: false,
    rejected: false
  
  }),
  methods:{
    async approve(company_mail,name){
      let url="http://127.0.0.1:8000/company/verification"
      let verify={
        company_mail:company_mail,
        admin_email:this.ndata.admin_email,
        name:name
      }
    let res= await this.$axios.post(url,verify)
    window.location.reload()
  },
    async deny(company_mail,name){
    console.log(company_mail,name)
    let url ="http://127.0.0.1:8000/company/verification"
    let reject={
      company_mail:company_mail,
      admin_email:this.ndata.admin_email,
      name:name,
      status:"rejected"
    }
    let res= await this.$axios.post(url,reject)
    window.location.reload()
  }
  }
  }
  </script>
  