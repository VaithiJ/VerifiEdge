<template>
    <v-container >
      <v-row>
       <v-col cols="12" sm="4">
        <v-container fluid>
          <v-card class="curved-box" elevation="12">
            <br /><br />
            <v-row justify="center">
              <v-col align-self="start" cols="12" class="d-flex justify-center align-center pa-0">
                <v-avatar
                  class="profile avatar-center-height avatar-shadow"
                  color="white"
                  size="170"
                >
                  <input
                    ref="uploader"
                    class="d-none"
                    type="file"
                    accept="image/*"
                    :change="onFileChanged"
                  />
                  <v-img
                    src="https://cdn-icons-png.flaticon.com/512/6915/6915987.png"
                  ></v-img>
                </v-avatar>
              </v-col>
            </v-row>
            <v-row>
              <notarydetails/>
            </v-row>
          </v-card>
          <br />

        </v-container>

        </v-col>
        <v-col cols="12" sm="6">

            <notaryprofile/>


    </v-col>
  </v-row>
            
    </v-container>
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
    })
}
</script>