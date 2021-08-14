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
    :items="albums"
    :search="search"
    class="elevation-1"
  >
<template  v-slot:item.album_image="{ item }">
<br>
<img  height="50" width="50" :src="item.album_image" alt=""/>
</template>
 
    <template v-slot:top>
      <v-toolbar flat color="">
        <v-toolbar-title>Collections</v-toolbar-title>
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
                    <v-text-field 
                    :rules="Rules"
                     v-model="editedItem.album_name" 
                     label="Collection Name"></v-text-field>

                  </v-col>
                  <v-col>
                  <v-btn 
                  :rules="Rules" 
                  class="primary mt-2 black--text" 
                  @click="album_image_attachment">
                  {{(!album_image.name) ? 'Collection Image' : album_image.name}}
                  
                  <v-icon right dark>
                    mdi-cloud-upload
                  </v-icon>
                  </v-btn>   
                  
                  <input 
                  required 
                  type="file" 
                  @change="check_album_image_attachment" 
                  style="display:none;" 
                  accept="image/*" 
                  ref="album_image_attachment_Input">
                  </v-col>
                  
                  </v-row>
                  <v-row>

                  <v-col>
                  <v-select
                  :rules="Rules"
                  v-model="editedItem.category_id" 
                  @change="get_data"
                  :items="cat_to_upload"
                  item-value="id"
                  item-text="category_name" 
                  label="Category"
                  ></v-select>
                  </v-col>
                  </v-row>

                  <v-row>
                  <v-col>
                  <v-select

                  :rules="Rules"
                  v-model="editedItem.sub_category_id" 
                  :items="sub_cat_to_upload"
                  item-value="id"
                  item-text="subcategory_name" 
                  label="Sub Category"
                  ></v-select>
                  
                  </v-col>
                    <v-col>
                  <v-select

                  :rules="Rules"
                  v-model="editedItem.brand_id" 
                  :items="brand_to_upload"
                  item-value="id"
                  item-text="brand_name" 
                  label="Brand"
                  ></v-select>
                  
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
    <template v-slot:item.product="{ item }">
      <v-icon
        small
         @click="product_by_album(item)"
      >
        mdi-eye
      </v-icon>
    </template>



        <template v-slot:item.action="{ item }">
      <v-icon
        small
        class="mr-2"
        @click="addProduct(item)"
      > 
      mdi-content-save
      </v-icon>
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
          text: 'Collection Name',
          align: 'left',
          sortable: false,
          value: 'album_name',
        },
        {
          text: 'Image',
          align: 'left',
          sortable: false,
          value: 'album_image',
        },
         {
          text: 'Brand',
          align: 'left',
          sortable: false,
          value: 'brand_name',
        },
         {
          text: 'Category',
          align: 'left',
          sortable: false,
          value: 'category_name',
        },
         {
          text: 'Sub Category',
          align: 'left',
          sortable: false,
          value: 'sub_category_name',
        },
          {
          text: 'Products',
          align: 'left',
          sortable: false,
          value: 'product',
        },
        { text: 'Actions', value: 'action', sortable: false },
      ],
      editedIndex: -1,
      album_image:{
        name:''
      },
      editedItem: {
        id:'',
        album_name:'',
        category_id:'',
        sub_category_id:'',
        brand_id:'',       
      },
      defaultItem: {
        id:'',
        album_name:'',
        category_id:'',
        sub_category_id:'',
        brand_id:'',   
      
      },
      response : {
        msg:''
      },
      albums:[],
      categories:[],
      cat_to_upload:[],
      sub_cat_to_upload:[],
      brand_to_upload:[],
      Rules : [
        v => !!v || 'This field is required',
      ],
    }),

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
      },
    },

    watch: {
      dialog (val) {
        val || this.close()
      },
    },

    async created () {
      
     const data = await this.$axios.get('albums');
     this.albums = data.data.albums
     this.cat_to_upload = data.data.categories;
            
    },

    methods: {

      product_by_album (item) {
         this.$router.push('album/product/' + item.id);
      },

      addProduct (item) {
         this.$router.push('album/' + item.id);
      },

      get_data () {
            this.$axios.get('get_data_for_albums/'+this.editedItem.category_id)
                .then((res) =>{
                this.sub_cat_to_upload = res.data.sub_categories 
                this.brand_to_upload = res.data.brands
                });
      },

      album_image_attachment () { 
      this.$refs.album_image_attachment_Input.click() 
      },

      check_album_image_attachment(e) { 

      this.album_image = e.target.files[0] || ''; 

      },

      editItem (item) {
          this.editedIndex = this.albums.indexOf(item)
          this.editedItem = Object.assign({}, item)
          this.dialog = true
          
      },

      deleteItem (item) {
        confirm('Are you sure you want to delete this item?') && 
         this.$axios.delete('albums/'+item.id)
            .then((res) => {

              if(res.data.response_status){ 

              const index = this.albums.indexOf(item)
              this.albums.splice(index, 1)
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
         

              let collection = new FormData();
              collection.append('album_name',this.editedItem.album_name);
              collection.append('album_image',this.album_image);
              collection.append('category_id',this.editedItem.category_id);
              collection.append('sub_category_id',this.editedItem.sub_category_id);
              collection.append('brand_id',this.editedItem.brand_id);

              // const payload = {
              //   album_name: this.editedItem.album_name,
              //   category_id:this.editedItem.category_id,
              //   sub_category_id:this.editedItem.sub_category_id,
              //   brand_id:this.editedItem.brand_id
              // }


           if (this.editedIndex > -1) {

            this.$axios.post('albums/' + this.editedItem.id, collection)
            .then(res => {
              console.log(res.data);
            if(res.data.response_status){  
              const index = this.albums.findIndex(item => item.id == this.editedItem.id)
              Object.assign(this.albums[index],res.data.updated_record);
              this.snackbar = res.data.response_status;
              this.response.msg = res.data.message;
              this.close()
              }
            })
            .catch(error => console.log(error));
           }
           else{
              
            this.$axios.post('albums',collection)
              .then((res) => {
               
              if(res.data.response_status){ 
            
              this.albums.unshift(res.data.new_record)
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