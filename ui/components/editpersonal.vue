<template>
  <v-container class="personalform">
    <v-alert border="top" color="red lighten-1" dismissible v-if="fail">Data insertion failed</v-alert>
    <v-form v-model="isFormValid">
      <br />
      <h3 class="text-center">Personal Data</h3>
      <br/><br/><br/>
      <v-row>
        <v-col cols="12" sm="6">
      <v-menu v-model="DatePicker" :close-on-content-click="false" :nudge-right="40" transition="scale-transition" offset-y min-width="auto">
        <template v-slot:activator="{ on }">
          <v-text-field v-model="dob" outlined prepend-icon="mdi-calendar" label="Date of Joining" readonly v-on="on"></v-text-field>
        </template>
        <v-date-picker v-model="dob" :max="today" no-title scrollable @input="saveDatePicker"></v-date-picker>
      </v-menu>
    </v-col>
    <v-col cols="12" sm="6">
      <v-text-field label="Mobile Number" outlined v-model="mob" prepend-icon="mdi-phone" :rules="[rules.required, rules.mob]"></v-text-field>
    </v-col>
    
    </v-row>
    <v-row>
    <v-col cols="12" sm="6">
      <v-text-field label="Company Name" outlined v-model="company" prepend-icon="mdi-domain" :rules="[rules.required,rules.company]"></v-text-field>
    </v-col>
    <v-col cols="12" sm="6">
      <v-text-field label="Company Email" outlined v-model="company_email" prepend-icon="mdi-email" :rules="[rules.required, rules.email]"></v-text-field>
    </v-col>
    
    </v-row>
    <v-row>
      
    <v-col cols="12" sm="6">
      <v-text-field label="Aadhaar" outlined v-model="aadhaar" prepend-icon="mdi-text-box" :rules="[rules.required, rules.aadhaar]"></v-text-field>
    </v-col>
    <v-col cols="12" sm="6">
      <v-file-input @change="fileselect" label="Upload Aadhaar Card" outlined :rules="[rules.required]"></v-file-input>
    </v-col>

    </v-row>
      <v-container class="text-center">
        <v-btn text color="blue lighten-1" @click="submit()" :disabled="!isFormValid" class="button">Submit</v-btn>
      </v-container>
      <v-container v-if="notallowed" class="text-center">
        <v-alert  type="error" dismissible> Only pdf is allowed </v-alert>
      </v-container>
    </v-form>
  </v-container>
</template>

<script>
export default {
  name: 'personaldata',
  async mounted() {
    this.email = this.$storage.getUniversal('Email');
    console.log(this.email);
  },
  data() {
    return {
      dob: "",
      email: "",
      company: "",
      company_email: "",
      mob: "",
      aadhaar: "",
      fail: null,
      isFormValid: null,
      notallowed: false,

      rules: {
        required: (v) => !!v || "Required",
            mob: (v) => v.match(/^[0-9]{10}$/) || "check your mobile number",
            aadhaar : (v) =>  v.match(/^\d{12}$/) || "Check the aadhaar number for errors",
            email : (v) => v.match(/^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/) || "Email format is wrong",
            company: (v) => v.match(/^[A-Za-z\s]+$/) || "No special Characters",
            company_email : (v) => v.match(/^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/) || "Email format is wrong",
            alphnum: (v) => v.match(/^[A-Za-z0-9\s]+$/) || "No special Characters",
            date : (v) => (v.match(/^\d{2}\/\d{2}\/\d{4}$/)) || "Date format is not correct"
      },
      menu: false,
      today: new Date().toISOString().substring(0, 10),
      DatePicker: false,
    };
  },
  methods: {
    async fileselect(event){
      this.file=event
    },
    async submit() {
      let formdata = new FormData()
            formdata.append('email',this.email)
            formdata.append('regno',this.aadhaar)
            formdata.append('file',this.file)
            let furl="http://127.0.0.1:8000/uploadfile/S3"
            let res = await this.$axios.post(furl,formdata,{ headers : {'Content-Type': 'application/json',}});
            if(res.data.pdf == false){
              this.notallowed = true
            }
            if (res.data == true){
      let nurl = "http://127.0.0.1:8000/user/expupdation"
        let ndata={
          'email': this.email
        }
        let nres = await this.$axios.post(nurl, ndata)
      let url = 'http://127.0.0.1:8000/personal/update';

      let pdata = {
        dob: this.dob,
        email: this.email,
        company_name: this.company,
        company_mail: this.company_email,
        mob: this.mob,
        aadhaar: this.aadhaar,
      };
      let result = await this.$axios.post(url, pdata);
      if (result.data === true) {
        this.$router.push('/user');
      } else {
        this.fail = true;
      }
    }},
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
    },
  },
};
</script>

<style>
.personalform {
  width: 100%;
  max-width: 600px; /* Adjust max-width as needed */
  margin: 0 auto;
  padding: 20px;
}
</style>