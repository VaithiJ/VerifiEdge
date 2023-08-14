<template>
  <v-container>
  <v-card flat
      max-width="775"
      class="mx-auto">
  <v-card-text>
    <h1><b>My info</b></h1>   
                <br>
<!--             profile image          -->
<v-card class="mx-auto" max-width="1000" tile>
  <v-img height="200" style="background-color: cyan;"></v-img>
  <v-row style="margin:2.5%;position:absolute; top: 130px">
              <v-list-item >
               <v-list-item-avatar size="100">
                  <img 
                    src="https://cdn-icons-png.flaticon.com/512/6915/6915987.png"
                    alt="John"
                  >
                </v-list-item-avatar>
                
              </v-list-item>
</v-row>
<br><br>

<v-row>
  <notarydetails/>
</v-row>
</v-card>
<br><br><br><br>

      
</v-card-text>
    </v-card>

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
