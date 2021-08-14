<template>
<v-app>
  <div class="text-center ma-2">

    <v-snackbar
      v-model="snackbar"
      :top="'top'"
    >
      {{response.msg}}
      <v-btn      
        text
        @click="snackbar = false"
      >
        Close
      </v-btn>
    </v-snackbar>
  </div>
  <v-data-table
    :headers="headers"
    :items="colors"
    :search="search"
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar flat color="">
        <v-toolbar-title>Colors</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
      
      <v-text-field
        v-model="search"
        label="Search"
        single-line
        hide-details
      ></v-text-field>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
       <v-dialog v-model="dialog" max-width="500px">
          <template v-slot:activator="{ on }">
            <v-btn color="primary"  class="mb-2" v-on="on">New Item</v-btn>
          </template>
          <v-form ref="form">
          <v-card>
            <v-card-title>
              <span class="headline">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col>
                    <v-text-field :rules="Rules" v-model="editedItem.color_name" label="Color Name"></v-text-field>
                  </v-col>
                  </v-row>
                  
                  <v-row>  
                  <v-col>
                    <v-text-field :rules="Rules" v-model="editedItem.color_code" label="Color Code"></v-text-field>
                  </v-col>
                  </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="close">Cancel</v-btn>
              <v-btn color="blue darken-1" text @click="save">Save</v-btn>
            </v-card-actions>
          </v-card>
          </v-form>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.action="{ item }">
      <v-icon
        small
        class="mr-2"
        @click="editItem(item)"
      >
        mdi-pencil
      </v-icon>
      <v-icon
        small
        @click="deleteItem(item)"
      >
        mdi-delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <!-- <v-btn color="primary" @click="initialize">Reset</v-btn> -->
    </template>
  </v-data-table>
  </v-app>
</template>
<script>
  export default {
   
    data: () => ({
      search:'',
      snackbar:false,
      dialog: false,
      headers: [
        {
          text: '#',
          align: 'left',
          sortable: false,
          value: 'id',
        },
         {
          text: 'Color Name',
          align: 'left',
          sortable: false,
          value: 'color_name',
        }, 
        {
          text: 'Color Code',
          align: 'left',
          sortable: false,
          value: 'color_code',
        },
        { text: 'Actions', value: 'action', sortable: false },
      ],
      editedIndex: -1,
      editedItem: {
        color_name: '',
        color_code: '',
       
      },
      defaultItem: {
        color_name: '',
        color_code: '',
      
      },
      response : {
        msg:''
      },
      colors:[],
       Rules : [
        v => !!v || 'This field is required',
      ],
    }),

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
      },
      // colors(){
        
      //   return this.$store.state.colors

      // }
    },

    watch: {
      dialog (val) {
        val || this.close()
      },
    },

    async created () {
     const colors = await this.$axios.get('colors');
     this.colors = colors.data;
            
    },

    methods: {

      editItem (item) {
          this.editedIndex = this.colors.indexOf(item)
          this.editedItem = Object.assign({}, item)
          this.dialog = true
          
      },

      deleteItem (item) {
        confirm('Are you sure you want to delete this item?') && 
         this.$axios.delete('colors/'+item.id)
            .then((res) => {

              if(res.data.response_status){ 

              const index = this.colors.indexOf(item)
              this.colors.splice(index, 1)
              this.snackbar = res.data.response_status;             
              this.response.msg = res.data.message;

              }
            });
      },

      close () {
        this.dialog = false
        setTimeout(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        }, 300)
      },

      save () {
        if(this.$refs.form.validate()){
           if (this.editedIndex > -1) {

            this.$axios.put('colors/' + this.editedItem.id, {
            color_name: this.editedItem.color_name,
            color_code: this.editedItem.color_code
            })
            .then(res => {
            if(res.data.response_status){ 
            const index = this.colors.findIndex(item => item.id == this.editedItem.id)
            this.colors.splice(index, 1,{
            id:this.editedItem.id,
            color_name:this.editedItem.color_name,
            color_code: this.editedItem.color_code
            });
              this.snackbar = res.data.response_status;
              this.response.msg = res.data.message;
              this.close()
              }
            })
            .catch(error => console.log(err));
           }
           else{
              
            this.$axios.post('colors',{
              color_name:this.editedItem.color_name,
              color_code: this.editedItem.color_code
              })
              .then((res) => {

              if(res.data.response_status){ 

              this.colors.push(res.data.new_record)
              this.snackbar = res.data.response_status;
              this.response.msg = res.data.message;

              this.close()
              
              }

            });
         
           }

        }
      },
    },
  }
</script>