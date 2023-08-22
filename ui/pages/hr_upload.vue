<template>
  <v-container  v-if="isAuthenticated">
    <v-row>
      <v-col class="d-flex justify-center align-center">
        <v-container>
          <upload/>
        </v-container>
      </v-col>
    </v-row>
  </v-container>
</template>
<script>
export default{
    layout:"hr_layout",
    async mounted(){
      const accessToken = localStorage.getItem('access_token');
    this.isAuthenticated = !!accessToken;
    },
    data: () => ({
      isAuthenticated: false
    })

}
</script>