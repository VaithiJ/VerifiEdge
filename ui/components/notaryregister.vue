<template>
    <v-container >
        <v-row align="center">
            <v-col cols="10" md="6">
                <div class="blue-bubble">
            <img
              data-aos="zoom-in"
              class="guide2Image"
              data-aos-duration="800"
              data-aos-delay="100"
              src="https://d1idiaqkpcnv43.cloudfront.net/website1.0/images/sign-up.png"
              alt="Guide Image"
              :style="{ width: '110%', height: 'auto' }"
            />
          </div>
            </v-col>
             <v-col cols="12" md="6">
                <v-card class="signin-card">
                 <v-form v-model="isFormValid">
                    <h1 class="text-center" >Registration Form</h1>
                      <v-alert dismissible type="error" v-model="fail"> You have already applied </v-alert>

              <v-container class="text-center">
              </v-container>
             <br/>

            <v-row>
                <v-col cols="12" sm="6">
                    <v-text-field outlined :textarea="true" class="text-field-in-box" required v-model="user.name" prepend-icon="mdi-account" label="Full Name" :rules="[rules.required, rules.name]"></v-text-field>
                </v-col>
                <v-col cols="12" sm="6">
                    <v-menu v-model="DatePicker" :close-on-content-click="false" :nudge-right="40" transition="scale-transition" offset-y min-width="auto">
                      <template v-slot:activator="{ on }">
                        <v-text-field v-model="user.dob" outlined prepend-icon="mdi-calendar" label="Date of Birth" readonly v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="user.dob" :max="today" no-title scrollable @input="saveDatePicker"></v-date-picker>
                    </v-menu>
                </v-col>
                <v-col cols="12" sm="6">
                    <v-select
                    outlined prepend-icon="mdi-account"
                    label="Gender" v-model="user.gender"
                    :items="['Male', 'Female']"
                  ></v-select>
                </v-col>
                <v-col cols="12" sm="6">
                    <v-text-field outlined :textarea="true" class="text-field-in-box" v-model="user.address" prepend-icon="mdi-email" label="Address" :rules="[rules.required]"></v-text-field>
                </v-col>
                <v-col cols="12" sm="6">
                    <v-text-field outlined :textarea="true" class="text-field-in-box" v-model="user.city" prepend-icon="mdi-email" label="City" :rules="[rules.required]"></v-text-field>
                </v-col>
                <v-col cols="12" sm="6">
                    <v-text-field outlined :textarea="true" class="text-field-in-box" v-model="user.state" prepend-icon="mdi-email" label="State" :rules="[rules.required]"></v-text-field>
                </v-col>
                <v-col cols="12" sm="6">
                    <v-text-field outlined :textarea="true" class="text-field-in-box" v-model="user.country" prepend-icon="mdi-email" label="Country" :rules="[rules.required]"></v-text-field>
                </v-col>
                <v-col cols="12" sm="6">
                    <v-text-field outlined :textarea="true" class="text-field-in-box" v-model="user.pincode" prepend-icon="mdi-email" label="Zip Code" :rules="[rules.required]"></v-text-field>
                </v-col>
                <v-col cols="12" sm="6">
                    <v-text-field outlined :textarea="true" class="text-field-in-box" v-model="user.mob" prepend-icon="mdi-phone" label="Mobile Number" :rules="[rules.required,rules.mob]"></v-text-field>
                </v-col>
                <v-col cols="12" sm="6">
                    <v-text-field outlined :textarea="true" class="text-field-in-box" v-model="user.qualification" prepend-icon="mdi-phone" label="Highesh Qualification" :rules="[rules.required]"></v-text-field>
                </v-col>
                <v-col cols="12" sm="6">
                    <v-text-field outlined :textarea="true" class="text-field-in-box" v-model="user.experience" prepend-icon="mdi-phone" label="Experience" :rules="[rules.required]"></v-text-field>
                </v-col>
                
                <v-col cols="12" sm="6">
                    <v-text-field outlined :textarea="true" class="text-field-in-box" v-model="user.email" prepend-icon="mdi-email" label="Admin Email" :rules="[rules.required,rules.email]"></v-text-field>
                </v-col>
                <v-col cols="12" sm="6">
                    <v-text-field outlined :textarea="true" class="text-field-in-box" v-model="user.aadhaar" prepend-icon="mdi-email" label="Aadhaar Number" :rules="[rules.required]"></v-text-field>
                </v-col>
                
                <v-col cols="12" sm="6">
                    <v-file-input @change="aadhaarFile = $event"  label="Upload Aadhaar Card" outlined :rules="[rules.required]"></v-file-input>
                  </v-col>
                  <v-col cols="12" sm="6">
                    <v-text-field outlined :textarea="true" class="text-field-in-box" v-model="user.pan" prepend-icon="mdi-email" label="PAN Number" :rules="[rules.required,rules.pan]"></v-text-field>
                </v-col>
                  <v-col cols="12" sm="6">
                    <v-file-input @change="panFile = $event"  label="Upload PAN Card" outlined :rules="[rules.required]"></v-file-input>
                  </v-col>
                  <v-col cols="12" sm="6">
                    <v-file-input @change="educationFile = $event"  label="Educational Certificate" outlined :rules="[rules.required]"></v-file-input>
                  </v-col>
                  <v-col cols="12" sm="6">
                    <v-text-field outlined :textarea="true" class="text-field-in-box" v-model="user.password" prepend-icon="mdi-lock" label="Password" type="password" :rules="[rules.required,rules.min]"></v-text-field>
                </v-col>
                  

            </v-row>
            <br/>
        
        <v-row justify="center">
        <v-btn text  color="blue lighten-1" @click="submit()" >Register</v-btn>

        </v-row>
        </v-form>
        <v-container v-if="sendotp">
            <v-form>
                <v-alert v-model="error" type="error"  dismissible>OTP verification failed</v-alert>
                <v-col>
                    <v-row>
                        <caption>Please enter the OTP below for verification</caption>
                    </v-row>
                    <v-row>
                        <v-text-field label ="Enter OTP Here" v-model="utop"></v-text-field>
                    </v-row>
                    <v-row>
                    </v-row>
                </v-col>
            </v-form>
        </v-container>
                </v-card>
    </v-col>
        </v-row>
    </v-container>

</template>
<script>

export default{
name:'signuppage',
layout:'signinlayout',
async mounted(){
    this.$vuetify.theme.dark=false;
},
data:() => ({
    user :{
        name :'',
        dob:"",
        email: '',
        aadhaar: '',
        password :'',
        gender:'',
        address:'',
        city:'',
        state:'',
        country:'',
        pincode:'',
        qualification:'',
        experience:'',
        mob:'',
        pan: '',        
    },
    success:false,
    fail:false,
    otp:'',
    utop:'',
    isFormValid:null,
    error:false,
    sendotp:null,
    aadhaarFile: null,
    panFile: null,
    educationFile: null,
    rules:{
        required: (v) => !!v || "Required",
        min : (v) =>  v.match(/^(?=.*[A-Z])(?=.*[@])(?=.*[a-z])(?=.*\d).{8,}$/)|| "Enter Password with One Cap letter ,@,small letter and with the number is required",
        email : (v) => v.match(/\S+@\S+\.\S+/) || "Email format is wrong",
        name: (v) => v.match(/^[A-Za-z\s]+$/) || "No special Characters in Name",
        mob: (v) => v.match(/^[0-9]{10}$/) || "check your mobile number",
        aadhaar : (v) =>  v.match(/^\d{12}$/) || "Check the aadhaar number for errors",
        pan : (v) => v.match(/^([A-Z]){5}([0-9]){4}([A-Z]){1}?$/) || "Check the PAN number for errors",
        
    },
    menu: false,
    today: new Date().toISOString().substring(0, 10),
    DatePicker: false,
}),
methods:{

    
    async home(){
        this.$router.push('/')
    },
    async uploadFiles(email, regno, file) {
      if (file) {
        const formData = new FormData();
        formData.append('email', email);
        formData.append('regno', regno);
        formData.append('file', file);

        try {
          await this.$axios.post("http://127.0.0.1:8000/uploadfile/S3", formData, {
            headers: {
              "Content-Type": "multipart/form-data",
            },
          });
          console.log("File uploaded successfully:", file.name);
        } catch (error) {
          console.error("Error uploading file:", error);
        }
      }
    },
    async submit(){

        let url = "http://127.0.0.1:8000/notary"
            await this.$axios.post(url,this.user).then(res => {
                if (res.data == true){
                    this.success = true
                    this.$router.push('/notarysignin')
                }
                else{
                    this.fail = true
                }
            });
        
        this.uploadFiles(this.user.email, this.user.aadhaar, this.aadhaarFile);
        this.uploadFiles(this.user.email, this.user.pan, this.panFile);
        this.uploadFiles(this.user.email, this.user.mob, this.educationFile);

    },
    async signup(){
            
            
        
    },
    saveDatePicker() {
      this.DatePicker = false;
    },
    getFormattedDate(date) {
      if (date) {
        const dateObj = new Date(date);
        const day = String(dateObj.getDate()).padStart(2, "0");
        const month = String(dateObj.getMonth() + 1).padStart(2, "0");
        const year = dateObj.getFullYear();
        return `${day}/${month}/${year}`;
      }
      return null;
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
    border-radius: 40px;
    box-shadow: 0px 0px 20px rgba(0, 0, 0, 0.1);
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
