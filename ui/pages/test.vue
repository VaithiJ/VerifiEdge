<template>
    <v-app >
      <v-main class="grey lighten-3">
        <v-container >
          <v-row>
            <v-col cols="12" md="3">
  
                <v-container fluid>
                  <v-card class="curved-box" elevation="12">
                    <br><br>
                    <v-row justify="center">
                      <v-col align-self="start" class="d-flex justify-center align-center pa-0" cols="12">
                        <v-avatar class="profile avatar-center-heigth avatar-shadow" color="white" size="170">
                          <input ref="uploader" class="d-none" type="file" accept="image/*" :change="onFileChanged">
                          <v-img src="https://cdn-icons-png.flaticon.com/512/6915/6915987.png"></v-img>
                        </v-avatar>
  
                      </v-col>
                      
                    </v-row>
                    <v-row>
                        <notarydetails/>
                    </v-row>
                  </v-card>
                </v-container>
            </v-col>
                
            <v-col cols="12" md="6">
                <notaryprofile/>
            </v-col>
            <v-col >
  
              <v-container fluid>
                <v-card max-width="350px" height="365px" class="mx-auto bg" elevation="2">
                  <br>
                  <v-row justify="center">
                    <v-card-title>Verifcation</v-card-title>
                  </v-row>
                  <br>
                  <v-container>
                    <adminNotaryaprroval/>

                  </v-container>

  
                </v-card>
              </v-container>
              <v-container class="text-center">
                <v-btn text outlined @click="back()" color="indigo darken-4">Go Back</v-btn>
              </v-container>
    
  
          </v-col>
          </v-row>
        </v-container>
      </v-main>
    </v-app>
  </template>
  <script>
export default{
  name :"NotaryyProfile",
    layout: "SuperAdmin_layout",
    async mounted(){
      const accessToken = localStorage.getItem('access_token');
    this.isAuthenticated = !!accessToken;
    },
    data: () => ({
    }),
    methods:{
        async back(){
            this.$router.push("/SuperAdminRequests")
        }
    }
}
</script>
  
  