<template>
    <v-container class="personalform" >
    <v-form v-model="formvalid" >
        <br/>
        <h3 class="text-center"> SSLC Details</h3>
        <v-alert border="top" color="red lighten-1" dismissible  v-if="fail"> Data insertion failed</v-alert>
        <v-alert class="success" dismissible v-if="success"> Data insertion succeeded</v-alert>
        <v-text-field label="Registration Number" v-model="sslc_regno" prepend-icon="mdi-notebook" :rules="[rules.required,rules.sslc_regno]"></v-text-field>
        <v-text-field label="Marks in %" v-model="sslc_marks" prepend-icon="mdi-brightness-percent" :rules="[rules.required,rules.percents]"></v-text-field>
        <v-text-field label="School" v-model="sslc_school" prepend-icon="mdi-town-hall" :rules="[rules.required,rules.sslc_school]"></v-text-field>
        <v-text-field label="Board" v-model="sslc_board" prepend-icon="mdi-school" :rules="[rules.required,rules.sslc_board]"></v-text-field>
       <v-select
        v-model="sslc_passout"
        :items="sslc_passout"
        label="Year of Completion"
        :rules="[rules.required]"
        prepend-icon="mdi-calendar"
      ></v-select>
        <v-file-input @change="fileselect"  label = "Upload Files" :rules="[rules.required]" ></v-file-input>
        <v-container class="text-center">
            <v-btn text  @click="submit()" :disabled="!formvalid" color="indigo lighten-2" class="button"> Submit </v-btn>
        </v-container>
    </v-form>
</v-container>
</template>
<script>
export default{
    name: "sslc",
    async mounted(){
        var url ='http://127.0.0.1:8000/user'
        this.generatesslc_passout();
        this.email= await this.$storage.getUniversal('Email');
        await this.$axios.get(url,{params:{email : this.email}}).then(res=>{
            this.name = res.data.name
        }).catch(error => console.log(error));

    },
    data : () => ({
        formvalid: null,
        sslc_regno : "",
        email : "",
        sslc_marks : "",
        sslc_school : "",
        success: null,
        name : "",
        fail : null,
        sslc_board : "",
        rules : {
            required: (v) => !!v || "Required",
            percents : (v) => (v>=0 && v<=100) || "Value must be between 0 and 100",
            email : (v) => v.match(/^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/) || "Email format is wrong",
            name : (v) => v.match(/^[A-Za-z\s]+$/) || "No special Characters in Name",
            sslc_regno : (v) => v.match(/^[a-zA-Z0-9]+$/) || "Register number format is wrong",
            sslc_school : (v) => v.match(/^[A-Za-z\s]+$/) || "No special Characters",
            sslc_board : (v) => v.match(/^[A-Za-z\s]+$/) || "No special Characters",
        },
        sslc_passout: [],
    }),
    created() {
  this.sslc_passout = this.generatesslc_passout();
    },
    methods:{
        async fileselect(event){
      this.file=event
    },
        async submit(){
            let url ="http://127.0.0.1:8000/sslc"
            let sslcdata = {
                sslc_regno : this.sslc_regno,
                email : this.email,
                sslc_marks : this.sslc_marks,
                sslc_school : this.sslc_school,
                sslc_passout : this.sslc_passout,
                name : this.name,
                sslc_board : this.sslc_board
            }
            let result = await this.$axios.post(url,sslcdata);
            console.log(result.data);
            let formdata= new FormData()
            formdata.append('email',this.email)
            formdata.append('regno',this.sslc_regno)
            formdata.append('file',this.file)
            let furl = "http://127.0.0.1:8000/uploadfile/S3"
            let res = await this.$axios.post(furl,formdata,{ headers : {'Content-Type': 'application/json',}});
            if (result.data == res.data){
                this.$router.push('/user')
            }else{
                this.fail= true
            }
        },
generatesslc_passout() {
  const currentYear = new Date().getFullYear();
  const startYear = currentYear - 50;
  const sslc_passout = [];

  for (let year = currentYear; year >= startYear; year--) {
    sslc_passout.push(year);
  }

  return sslc_passout;
}

    }

}
</script>
