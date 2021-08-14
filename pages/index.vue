<template>
<div>
<v-row>
  <v-col>
      <v-card>
    <v-list-item three-line>
      <v-list-item-content>
        <div class="overline mb-4">Products</div>
        <v-list-item-title class="headline mb-1">Total </v-list-item-title>
        <v-list-item-subtitle>
          </v-list-item-subtitle>
      </v-list-item-content>

      <v-list-item-avatar
        tile
        size="50"
        color="white"
        >
       <span class="black--text">05</span>
        </v-list-item-avatar>
    </v-list-item>


    
  </v-card>
  </v-col>
  <v-col>
      <v-card>
    <v-list-item three-line>
      <v-list-item-content>
        <div class="overline mb-4">Categories</div>
        <v-list-item-title class="headline mb-1">Total </v-list-item-title>
        <v-list-item-subtitle>
          </v-list-item-subtitle>
      </v-list-item-content>

      <v-list-item-avatar
        tile
        size="50"
        color="white"
        >
       <span class="black--text">05</span>
        </v-list-item-avatar>
    </v-list-item>


    
  </v-card>
  </v-col>

    <v-col>
      <v-card>
    <v-list-item three-line>
      <v-list-item-content>
        <div class="overline mb-4">Sub Categories</div>
        <v-list-item-title class="headline mb-1">Total </v-list-item-title>
        <v-list-item-subtitle>
          </v-list-item-subtitle>
      </v-list-item-content>

      <v-list-item-avatar
        tile
        size="50"
        color="white"
        >
       <span class="black--text">05</span>
        </v-list-item-avatar>
    </v-list-item>


    
  </v-card>
  </v-col>

    <v-col>
      <v-card>
    <v-list-item three-line>
      <v-list-item-content>
        <div class="overline mb-4">Brands</div>
        <v-list-item-title class="headline mb-1">Total </v-list-item-title>
        <v-list-item-subtitle>
          </v-list-item-subtitle>
      </v-list-item-content>

      <v-list-item-avatar
        tile
        
        size="50"
        color="white"
        >
       <span  class="black--text">789</span>
        </v-list-item-avatar>
    </v-list-item>


    
  </v-card>
  </v-col>
</v-row>


  </div>
</template>

<script>
export default{
  data(){
    return {
     
    }
  },
created () {
      this.initialize()
    },




   methods: {

      async initialize () {

      },
   }
   }
</script>