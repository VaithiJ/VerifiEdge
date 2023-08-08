<template>
  <v-container>
    <v-container v-if="show">
      <v-card class="mx-auto my-12"
    max-width="700">
      <v-list>
        <v-list-item
          v-for="request in paginatedRequests"
          :key="request.email"
        >
          <v-list-item-title v-text="request.name"></v-list-item-title>
          <v-list-item-subtitle v-text="request.email"></v-list-item-subtitle>
          <v-btn text color="#3498db" @click="view(request.email)">view
            <!-- <v-icon color="#3498db">mdi-card-account-details-outline</v-icon> -->
          </v-btn>
          <v-btn icon @click="approve(request.email)">
            <v-icon color="green">mdi-check</v-icon>
          </v-btn>
        </v-list-item>
      </v-list>
      </v-card>
      <v-container class="text-center">
        <v-pagination
          v-model="currentPage"
          :total-visible="5"
          :length="totalPages"
          @input="changePage"
        ></v-pagination>
      </v-container>
    </v-container>
    <v-container v-if="hide">
      <h2 class="text-center" style="color: darkblue;">No Profiles</h2>
    </v-container>
  </v-container>
</template>

<script>
export default {
  name: "NotaryInProgress",

  data() {
    return {
      currentPage: 1,
      pageSize: 5,
      profiles: [],
      requests: {},
      count: {},
      notary_email: null,
      notary_name: null,
      wallet: 0,
      show: false,
      hide: false
    };
  },

  async mounted() {
    this.$vuetify.theme.dark = false;
    await this.fetchRequests();
  },

  computed: {
    totalPages() {
      return Math.ceil(this.requests.length / this.pageSize);
    },
    paginatedRequests() {
      const startIndex = (this.currentPage - 1) * this.pageSize;
      const endIndex = startIndex + this.pageSize;
      return this.requests.slice(startIndex, endIndex);
    },
  },

  methods: {
    async fetchRequests() {
      try {
        const response = await this.$axios.get("http://127.0.0.1:8000/inprogressuser");
        this.requests = response.data.list;
        this.show = this.requests.length > 0;
        this.hide = !this.show;
      } catch (error) {
        console.error(error);
      }
    },

    changePage(page) {
      this.currentPage = page;
    },

    async view(email) {
      this.$storage.setUniversal("user_email", email);
      this.$router.push("/userprofile");
    },

    async approve(email) {
      try {
        const url = "http://127.0.0.1:8000/inprogress_verified";
        const data = {
          email: email,
        };
        const response = await this.$axios.post(url, data);
        window.location.reload();
      } catch (error) {
        console.error(error);
      }

      let rurl = "http://127.0.0.1:8000/send_rejection_email"
        let rdata={
          email: email,
          email_subject: "Approval Mail",
          email_body: `
          Dear Applicant,

          We are here to inform you that your Data has approved by the notary.

          We appreciate your interest in our services and your effort in applying. Thank you for considering us.

          With Regards,

          VerifiEdge

          `
        }
        let nres = await this.$axios.post(rurl, rdata)
    },
  },
};
</script>