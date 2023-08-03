<template>
  <v-container fluid class="d-flex align-center justify-center">
    <v-card class="text-center" :elevation="6" :height="auto" :width="700">
      <br />
      <v-text-title><h2>User Data</h2></v-text-title>
      <br />
      <v-row>
        <v-col>
          <v-container>
            <v-btn size="40%" color="blue lighten-1" dark @click="downloadTemplate()">Download Template</v-btn>
          </v-container>
        </v-col>
      </v-row>
      <v-divider></v-divider>
      <v-divider></v-divider>
      <v-divider></v-divider>
      <v-row>
        <v-col>
          <v-container class="text-center">
            <v-file-input @change="onFileChange" style="width:70%; margin:0 auto; " label="File input" variant="solo-filled"></v-file-input>
            <v-btn size="20%" :loading="isLoading" :disabled="isLoading || !file" color="blue lighten-1" dark @click="upload()">Upload</v-btn>
            <table v-if="excelData">
              <tr v-for="(row, rowIndex) in excelData" :key="rowIndex">
                <td v-for="(cell, cellIndex) in row" :key="cellIndex">{{ cell }}</td>
              </tr>
            </table>
          </v-container>
        </v-col>
      </v-row>
      <v-row>
        <v-container class="text-center">
          <h3 v-if="excelData && excelData.length">Uploaded Details:</h3>
        </v-container>
      </v-row>
    </v-card>
  </v-container>
</template>

<script>
import axios from "axios";

export default {
  name: 'hseupload',
  data() {
    return {
      isLoading: false,
      file: null,
      excelData: null,
    };
  },
  methods: {
    onFileChange(event) {
      this.file = event.target.files[0];
    },
    async upload() {
      try {
        const formData = new FormData();
        formData.append("file", this.file);

        const response = await axios.post("http://127.0.0.1:8000/upload", formData, {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        });

        this.uploadedFile = response.data.filename;
      } catch (error) {
        console.error("Error uploading file:", error);
      }
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
    }
  },
};
</script>
