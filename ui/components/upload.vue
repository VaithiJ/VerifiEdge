<template>
  <v-container fluid class="d-flex align-center justify-center">
    <v-row>
      <v-col  >
          
          <v-row>
            <v-col>
              <v-container class="text-center">
                <br>
          <v-text-title><h2>Download the Template</h2></v-text-title>
          <br><br>
                <v-btn size="40%" color="indigo darken-4" style="color: white" @click="downloadCSVTemplate">
                  Personal Template
                </v-btn>
              </v-container>
            </v-col>
          </v-row>
          <br><br>

          <v-row>
            

            <v-col>
              <v-container class="text-center">
                <v-text-title><h2>Upload the User data</h2></v-text-title>
                <br>
                <table class="note-table">
                  <tr>
                    <td class="note-cell">
                      <v-text-title>NOTE:</v-text-title>
                      <p>Please check the format of the data in CSV File before uploading</p>
                      <ul>
                        <li>Mobile number and Aadhaar number should be in the Number format.</li>
                        <li>Date of Birth (DOB) should be in Date(Short Date) format (DD-MM-YYYY).</li>
                      </ul>
                    </td>
                  </tr>
                </table>
                <br>

                <v-file-input outlined size="" @change="fileselect" style="width:30%; margin:0 auto; " label="File input" variant="solo-filled"></v-file-input>
                <v-btn size="20%" :loading="isLoading" :disabled="isLoading" color="indigo darken-4" style="color:white" @click="upload()">Upload</v-btn>
              </v-container>
            </v-col>
          </v-row>
          
          <v-row>
            <v-container class="text-center">
              <h3 v-if="this.data.total_count">Uploaded Details:</h3>
              <v-row>
              <v-col>
                <v-container>
                  <v-card-subtitle v-if="this.data.total_count">Total Uploaded ID: {{ this.data.total_count }}</v-card-subtitle>
                </v-container>
              </v-col>
              <v-col>
                <v-container>
                  <v-card-subtitle v-if="this.data.insert_count">Validated ID: {{ this.data.insert_count }}</v-card-subtitle>
                </v-container>
              </v-col>
              <v-col>
                <v-container>
                  <v-card-subtitle v-if="this.data.delete_count">Invalid ID: {{ this.data.delete_count }}</v-card-subtitle>
                </v-container>
              </v-col>
              </v-row>
             
            </v-container>
            
          </v-row>
          <v-row v-if="this.data.delete_list.length > 0">
            <v-col>
              <v-container class="text-center">
                <h3>Invalid ID List:</h3>
                <h5>Please check and reupload these Invalid ID details in proper fomrat</h5>
                <br>
                <table class="styled-table">
                  <thead>
                    <tr>
                      <th class="styled-table-header">Name</th>
                      <th class="styled-table-header">Email</th>
                      <th class="styled-table-header">Mobile No</th>
                      <th class="styled-table-header">DOB</th>
                      <th class="styled-table-header">Aadhaar</th>
                      <th class="styled-table-header">Comapny Name</th>
                      <th class="styled-table-header">Company mail</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="(item, index) in data.delete_list" :key="index">
                      <td>{{ item.name }}</td>
                      <td>{{ item.email }}</td>
                      <td>{{ item.mob }}</td>
                      <td>{{ item.dob }}</td>
                      <td>{{ item.aadhaar }}</td>
                      <td>{{ item.company_name }}</td>
                      <td>{{ item.company_mail }}</td>

                      <!-- Add more columns here based on your deleted_list structure -->
                    </tr>
                  </tbody>
                </table>
              </v-container>
            </v-col>
          </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>
<script>
export default {
name: 'personalupload',
data: () => ({
    data:{
      delete_list: [], // Initialize the delete_list array

    },
    isLoading: false,

  }),
methods:{
  async fileselect(event){
      this.file=event
    },
  async upload(){
      this.isLoading = true;

      let formdata= new FormData()
          formdata.append('csv_file',this.file)
          let furl = "http://127.0.0.1:8000/hr/uploadpersonal"
          let res = await this.$axios.post(furl, formdata);
          this.data = res.data
          let total_count = res.data.total_count
          let insert_count = res.insert_count
          let delete_count = res.delete_count
          console.log(res.data.total_count)

          // Simulate an asynchronous operation, such as an API call
          setTimeout(() => {
            // After the operation is complete, set isLoading to false
            this.isLoading = false;
          }, 2000);
  },
  downloadCSVTemplate() {
    const csvContent ="name,email,mob,dob,company_name,company_mail,aadhaar"
    const blob = new Blob([csvContent], { type: "text/csv;charset=utf-8;" });
    const url = URL.createObjectURL(blob);
    const link = document.createElement("a");
    link.href = url;
    link.download = "personal.csv";
    link.style.visibility = "hidden";
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
    URL.revokeObjectURL(url);
  },
  showDeletedList() {
      // Display the table with deleted data list
      this.data.showDeletedList = true;
    },
}
}
</script>
<style scoped>

/* ... (previous styles) ... */
.note-table {
  width: 40%;
  margin: 0 auto; /* Center alignment */
  border-collapse: collapse;
  border-spacing: 0;
  margin-bottom: 20px;
}

.note-cell {
  padding: 20px;
  text-align: left;
  background-color: #f0f0f0;
  border: 1px solid #ccc;
}

/* ... (remaining styles) ... */

/* Mobile responsiveness */
@media screen and (max-width: 600px) {
  .note-table {
    width: 80%; /* Adjust as needed */
  }
}

/* ... (remaining styles) ... */

.styled-table {
  width: 100%;
  border-collapse: collapse;
  border-spacing: 0;
  border: 1px solid #ccc;
}

.styled-table th,
.styled-table td {
  padding: 10px;
  text-align: center;
}

.styled-table-header {
  background-color: #f0f0f0;
}

.styled-table tbody tr:nth-child(even) {
  background-color: #f2f2f2;
}

.styled-no-profiles {
  color: darkblue;
}

/* Adjust the button color */
.v-btn.primary {
  color: #fff;
  background-color: #3498db;
}
</style>