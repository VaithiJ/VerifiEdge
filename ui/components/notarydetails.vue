<template>
    <v-container style="width: 80%; " v-if="data_s">
  
  
          <v-container>
            <v-row>
              <v-col style="padding-left: 4%;">
                <h3 class="text-title-1">Name</h3>
                <h3 class="text-subtitle-1">{{ pdata.name }}</h3>
                <h3 class="text-title-1">Mobile Number</h3>
                <h3 class="text-subtitle-1">{{ pdata.mob }}</h3>
                <h3 class="text-title-1">Email ID</h3>
                <h3 class="text-subtitle-1">{{ pdata.email }}</h3>
  
              </v-col>
  
            </v-row>
          </v-container>
  
  
    </v-container>
  </template>
  
  <script>
  export default{
  name: 'userprofile',
  async mounted (){
    this.$vuetify.theme.dark =false;
    this.email = this.$storage.getUniversal('notary_email')
    let url = "http://127.0.0.1:8000/notary"
    let res = await this.$axios.get(url,{params:{ email :this.email}});
    this.pdata=res.data
    console.log(this.pdata)
    if(this.pdata == false){
      this.data_s = false
    }
    else{
      this.data_s = true
    }
    if (this.pdata.status == "pending"){
      this.pending = true
      this.verified = false
    }
    if(this.pdata.status == "verified"){
      this.verified = true
      this.pending = false
    }
  
  
  
  },
  data: () =>({
    email:"",
    pdata :{},
    data_s: false,
    pending: false,
    verified: false,
  }),
  }
  </script>
  