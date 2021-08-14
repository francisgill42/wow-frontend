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


  <template  v-slot:item.product_images="{ item }">

      <v-dialog v-model="dialog1" persistent>
      <template v-slot:activator="{ on }">
        <v-btn color="primary" dark v-on="on">Open Gallery</v-btn>
      </template>
        <v-card>
        <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn text @click="dialog1 = false">X</v-btn>
        </v-card-actions>
        <v-carousel hide-delimiters>

        <v-carousel-item
        v-for="i in item.product_images" :key="i"
        :src="i"
        ></v-carousel-item>
        </v-carousel>

        </v-card>
    </v-dialog>


  </template>

  <template v-slot:top>
  <v-toolbar flat color="">
  <v-toolbar-title>Products</v-toolbar-title>
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

  <v-dialog v-model="dialog" max-width="500px">
  <!-- <template v-slot:activator="{ on }">
  <v-btn color="primary"  class="mb-2" v-on="on">New Item</v-btn>
  </template> -->
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
  v-model="editedItem.product_title" 
  label="Product Title"></v-text-field>

  </v-col>

    <v-col>
        <v-text-field :rules="Rules" v-model="editedItem.product_code" label="Product CODE"></v-text-field>
</v-col>

  <!-- <v-col>
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
  </v-col> -->

  </v-row>
  <v-row>
<v-col>
<v-text-field type="number" :rules="Rules"  v-model="editedItem.product_price" label="Product Price"></v-text-field>
</v-col>
<v-col>
<v-select
v-model="editedItem.color_id" 
:items="['red','green','blue']"
item-value="id"
item-text="color" 
label="Color"
></v-select>
</v-col>
</v-row>
<v-row>

      <v-file-input

      small-chips
      show-size
      multiple
      clearable
      @change="check_attachment"
    >
      <template v-slot:selection="{ text }">
        <v-chip
          small
          text-color="black"
          color="primary" 
          >
          {{ text }}
        </v-chip>
      </template>
    </v-file-input>
</v-row>
<v-row>
<v-col>
<v-textarea  v-model="editedItem.product_description" label="Production Description"></v-textarea>
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
  <!-- <v-icon
  small
  class="mr-2"
  @click="editItem(item)"
  >
  mdi-pencil
  </v-icon> -->
  <v-icon
  small
  @click="deleteItem(item)"
  >
  mdi-delete
  </v-icon>
  </template>
  </v-data-table>
  </v-app>
  </template>

  <script>
  export default {

  data: () => ({
  collection_id:'',
  search:'',
  snackbar:false,
  dialog: false,
  dialog1: false,
  headers: [
  {
  text: '#',
  align: 'left',
  sortable: false,
  value: 'id',
  },
  {
  text: 'Product Title',
  align: 'left',
  sortable: false,
  value: 'product_title',
  },
    {
  text: 'Product URL',
  align: 'left',
  sortable: false,
  value: 'url',
  },
    {
  text: 'Product Code',
  align: 'left',
  sortable: false,
  value: 'product_code',
  },
  {
  text: 'Product Price',
  align: 'left',
  sortable: false,
  value: 'product_price',
  },
    {
  text: 'Product Description',
  align: 'left',
  sortable: false,
  value: 'product_description',
  },
  {
  text: 'Image',
  align: 'left',
  sortable: false,
  value: 'product_images',
  },
  { text: 'Actions', value: 'action', sortable: false },
  ],
  editedIndex: -1,
  album_image:{
  name:''
  },
  item : {
    product_image:[],
  },
  editedItem: {
    color_id:'',
    product_code:'',
    product_title:'',
    product_price:'',
    product_description:'',
   },
  defaultItem: {
    color_id:'',
    product_code:'',
    product_title:'',
    product_price:'',
    product_description:'',
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
  dialog1 (val) {
  val || this.close()
  },
  },

  async created () {

  this.collection_id = this.$route.params.album_id;
  const data = await this.$axios.get(`albums/${this.collection_id}/products`);
  this.albums = data.data;



  this.cat_to_upload = data.data.categories;

  },

  methods: {

  product_by_album (item) {
  this.$router.push('album/products/' + item.id);
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
  this.$axios.delete('product/'+item.id)
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
check_attachment(e) { 
  if(!e.length){
    return false;
  }
    for( var i = 0; i < e.length; i++ ){
          this.item.product_image.push( e[i] ); 
    }


},

  save () {
  if(this.$refs.form.validate()){

let product = new FormData();  


product.append('collection_id',this.collection_id);
product.append('color_id',this.item.color_id);
product.append('product_code',this.item.product_code);
product.append('product_title',this.item.product_title); 
product.append('product_price',this.item.product_price);
product.append('product_description',this.item.product_description);

  for(var j = 0; j < this.item.product_image.length; j++){
     product.append('product_images['+j+']', this.item.product_image[j]);  
  }  

  if (this.editedIndex > -1) {

  this.$axios.post('product/' + this.editedItem.id, product)
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