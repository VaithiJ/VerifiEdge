<template>
  <v-container fluid class="d-flex align-center justify-center">
    <v-card class="text-center" :elevation="6" :height="auto" :width="700">
      <br/>
      <v-text-title><h2>User Data</h2></v-text-title>
      <br/>
      <v-row>
        <v-col key="download-btn">
          <v-container>
            <v-btn color="blue lighten-1" dark @click="downloadTemplate()">Download Template</v-btn>
          </v-container>
        </v-col>
      </v-row>
      <br/><br/>
      <v-divider></v-divider>
      <v-divider></v-divider>
      <br/><br/>
      <v-row>
        <v-col>
          <v-container class="text-center">
            <v-file-input @change="onFileChange" style="width:70%; margin:0 auto; " label="Upload Template" outlined variant="solo-filled"></v-file-input>
            <v-btn :loading="isLoading" :disabled="isLoading || !file" color="blue lighten-1" dark @click="upload()">Upload</v-btn>
            <template v-if="excelData">
              <table>
                <tr v-for="(row, rowIndex) in excelData" :key="rowIndex">
                  <td v-for="(cell, cellIndex) in row" :key="cellIndex">{{ cell }}</td>
                </tr>
              </table>
            </template>
          </v-container>
        </v-col>
      </v-row>
      <v-row>
        <v-container class="text-center">
          <h3 v-if="excelData && excelData.rowIndex">Uploaded Details:</h3>
        </v-container>
      </v-row>
    </v-card>
  </v-container>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      isLoading: false,
      file: null,
      excelData: null,
    };
  },
  methods: {
    async onFileChange(event){
        this.file=event
      },
    async downloadTemplate() {
      try {
        const response = await axios.get(
          "http://localhost:8000/download_excel/template.xlsx",
          { responseType: "blob" }
        );

        const url = window.URL.createObjectURL(new Blob([response.data]));
        const link = document.createElement("a");
        link.href = url;
        link.setAttribute("download", "template.xlsx"); 
        document.body.appendChild(link);
        link.click();
        window.URL.revokeObjectURL(url);
      } catch (error) {
        console.error(error);
        alert("Error downloading file");
      }
    },
    async upload() {
    try {
      this.isLoading = true;
      const formData = new FormData();
      formData.append('file', this.file);
      const response = await axios.post('http://127.0.0.1:8000/upload', formData);
      const data = response.data;
      this.excelData = data;
      this.isLoading = false;
      alert("File successfully uploaded");
    } catch (error) {
      console.error(error);
      alert("Error uploading file");
      this.isLoading = false;
    }
  },
  
  },
};
</script>
