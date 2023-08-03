<template>
    <v-container>
        <v-row align="center">
       
        <v-col cols="12" md="6">
          <v-card class="signin-card">
        <v-form>
            <h1 class="text-center"> Company Signin </h1>
            <v-alert border="top" color="red lighten-1" dismissible  v-if="fail"> Data insertion failed</v-alert>
            <v-container class="text-center">
              </v-container>
            <v-row>
                <v-col cols="12">
              <v-text-field   outlined :textarea="true" class="text-field-in-box" v-model="company_mail" prepend-icon="mdi-email" :rules="[rules.required,rules.email]" label ="Company Email"></v-text-field>
            </v-col>
           
            <v-col cols="12">
              <v-text-field outlined :textarea="true" class="text-field-in-box" v-model="password" type="password" prepend-icon="mdi-lock" :rules="[rules.required,rules.min]" label="Password"></v-text-field>
            </v-col>
            </v-row>
            <br>
            <v-row justify="center">
                <v-btn text @click="hrlogin()" color="blue lighten-1" width="200px">Signin</v-btn>
            </v-row>
        </v-form>
          </v-card>
        </v-col>
        <v-col cols="14" md="6">
          <div class="blue-bubble">
            <img
              data-aos="zoom-in"
              class="guide2Image"
              data-aos-duration="800"
              data-aos-delay="100"
              src="https://png.pngtree.com/png-vector/20220721/ourmid/pngtree-sales-team-with-financial-business-growth-development-from-people-working-and-png-image_6014803.png"
              alt="Guide Image"
              :style="{ width: '170%', height: 'auto' }"

            />
          </div>
        </v-col>
        </v-row>
            </v-container>
       
</template>
<script>
export default{
    name : "hrlogin",
    data : () =>({
        company_mail : "",
        password : "",
        fail: null,
        rules : {
            required: (v) => !!v || "Required",
            min : (v) =>  v.match(/^(?=.*[A-Z])(?=.*[@])(?=.*[a-z])(?=.*\d).{8,}$/)|| "Enter Password with One Cap letter ,@,small letter and with the number is required",
            email : (v) => v.match(/\S+@\S+\.\S+/) || "Email format is wrong",
        }
    }),
    methods:{
        async hrlogin(){
            

            let url = "http://127.0.0.1:8000/hr/login";
            let nlogin = {
                company_mail: this.company_mail,
                password: this.password
            }
            let result = await this.$axios.get(url, {params:{'company_mail': this.company_mail, 'password': this.password}});
            if (result.data === true) {
                this.$storage.setUniversal('hrmail',this.company_mail)
                this.$router.push('/hrpage');
            }else{
                this.fail = true;
            }
            let nurl = "http://127.0.0.1:8000/hr/login_date"
       
            let nres =  await this.$axios.post(nurl, nlogin)
            
            
            
        }
    }
}
</script>
<style>  
  .signin-card {
    width: 100%;
    max-width: 600px;
    margin: 0 auto;
    padding: 20px;
  }
  
  .text-field-in-box {
    width: 100%;
  }
  
  .guide2Image {
    width: 100%;
    overflow: hidden;
  }
  .img-resize {
  max-width: 600%; 
  max-height: 600px;
  }
  
  @keyframes blueBubble {
    0% {
      transform: translateY(0);
    }
    50% {
      transform: translateY(-20px);
    }
    100% {
      transform: translateY(0);
    }
  }
  
  .blue-bubble {
    position: relative;
    display: inline-block;
    animation: blueBubble 2s infinite;
  }
</style>
