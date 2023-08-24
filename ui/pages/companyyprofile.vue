<template>
    <v-container>
        <v-row>
        <v-col>
          <br/>
          <h2 style="color: black;" align="center">Company Profile</h2>
          <br/><br/>  
            <v-container fluid >
              <v-card max-width="450px" class="mx-auto bg" style="border-color: blue;" elevation="5">
                <br><br>
                <v-row justify="center">
                  <v-col align-self="start" class="d-flex justify-center align-center pa-0" cols="12">
                    <v-avatar class="profile avatar-center-heigth avatar-shadow" color="white" size="164">

                      <input ref="uploader" class="d-none" type="file" accept="image/*" :change="onFileChanged">
                      <v-img src="https://cdn-icons-png.flaticon.com/512/6915/6915987.png"></v-img>


                    </v-avatar>

                  </v-col>

                </v-row>
                <vrow>
                  <CompanyProfile/>
                </vrow>

              </v-card>
            </v-container>

        </v-col>
      </v-row>
    </v-container>
</template>
<script>
export default{
  name :"companyyprofile",
    layout: "admin_layout",
}
</script>
  