<template>
    <v-container>
      <br>
      <h2 style="color: black;" align="center" >Company Profiles</h2>
        <v-container>
  
          <v-col>
                <v-container>
                <v-tabs
                v-model="tabs"
                centered
              >
                <v-tab
                  v-for="n in 1"
                  :key="n"
                >
                  pending 
                </v-tab>
                <v-tab
                  v-for="n in 1"
                  :key="n"
                >
                  Approved
                </v-tab>
              </v-tabs>
              </v-container>
              </v-col>
              
    
              <v-container style="margin-bottom: 50%;">
                  <v-tabs-items v-model="tabs">
            <v-tab-item>
              <v-card>
                <v-card-text>
                    <CompanyPending/>
    
                </v-card-text>
              </v-card>
            </v-tab-item>
            
            <v-tab-item>
              <v-card>
                <CompanyApproved/>
              </v-card>
            </v-tab-item>
          </v-tabs-items>   
              </v-container>
        </v-container>
    </v-container>
  </template>
  <script>
  export default {
    name :"notary",
    layout: "admin_layout",
    
    data: () => ({
        tabs: null,
      }),
  
  }
  </script>
  
  