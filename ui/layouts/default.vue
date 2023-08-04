<template>
<v-app index >
<v-app-bar app color="#3498db">
    <img class="mr-3" :src="require('../assets/blockedge-logo.svg')" height="40"/>
    <v-spacer></v-spacer>
    <v-btn text href="#hero"  color="black" width="100px">Home</v-btn>
    &ensp;&ensp;
    <v-btn text href="#about" color="black" width="100px">About</v-btn>
    &ensp;&ensp;
    <v-btn text href="#features"  color="black" width="100px">Features</v-btn>
    &ensp;&ensp;&ensp;
    <v-btn text href="#contact"  color="black" width="100px">Contact</v-btn> 
   &ensp;&ensp;&ensp;
   <a href="/getstarted">
    <v-btn  rounded   color="blue lighten-5" class="text =white" width="120px" >Get Started</v-btn>

   </a>
</v-app-bar>
<v-main>
    <Nuxt/>
</v-main>

<v-footer
      color="#3498db" style="border"
      dark
    >
      <v-row
        justify="center"
        class="my-4 text-center"
      >
        <v-col cols="12" style="color:black"
          >Copyright @ Securekloud Technologies Ltd {{ new Date().getFullYear() }}</v-col
        >
        <v-col cols="12" style="width: 10%; ">
          <v-btn style="width: 5%; color: black;"
            v-for="icon in icons"
            :key="icon"
            text
            dark
          >
            <v-icon>{{ icon }}</v-icon>
          </v-btn>
        </v-col>
        <v-col
          cols="12"
          class="align-center" style="color:black"
          >&copy; 2023 VerifiEdge</v-col>
      </v-row>
    </v-footer>
</v-app>
</template>
<script>
  export default {
    name : 'indexlayout',
    async mounted(){
      this.$vuetify.theme.dark=false
    },
    data: () => {
    return {
      icons: [
        'mdi-facebook',
        'mdi-twitter',
        'mdi-instagram',
        'mdi-youtube',
      ],
    };
  },
    methods : {
        async signup() {
            this.$router.push("/signup");
        },
        async signin() {
            this.$router.push("/signin");
        },
        async getstarted(){
          this.$router.push("/getstarted");

        }
    }
}

</script>
