<template>
<v-app>

<v-snackbar
v-model="snackbar"
:top="'top'"
>
{{msg}}
<v-btn      
text
@click="snackbar = false"
>
Close
</v-btn>
</v-snackbar>


<v-card>


<v-toolbar flat dark>
<v-toolbar-title class="headline">Create</v-toolbar-title>
<v-spacer></v-spacer>
<v-btn
color="primary"
class=" black--black"
@click="save"
:loading="loading"
>
Submit
</v-btn>
</v-toolbar>

<v-card-text>
<v-form ref="form" lazy-validation>

<!-- v-for="(loop,index) in i" :key="index" -->
<v-container>
<v-row>
<v-col>
<strong>
<v-alert class="primary">
Product 

</v-alert>
</strong>
</v-col>

</v-row>
<v-row>

<v-col>
<v-text-field :rules="Rules" v-model="item.product_title" label="Product Title"></v-text-field>
</v-col>

<v-col>
<v-text-field :rules="Rules" v-model="item.product_code" label="Product CODE"></v-text-field>
</v-col>
</v-row>

<v-row>
<v-col>
<v-text-field type="number" :rules="Rules"  v-model="item.product_price" label="Product Price"></v-text-field>
</v-col>
<v-col>
<v-select
v-model="item.color_id" 
:items="colors"
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
      <!-- <template v-slot:selection="{ text, inner_index, file }">
        <v-chip
          small
          text-color="black"
          color="primary" 
          >
          {{ text }}
        </v-chip>
      </template> -->
    </v-file-input>
</v-row>
<v-row>
<v-col>
<v-textarea  v-model="item.product_description" label="Production Description"></v-textarea>
</v-col>
</v-row>
<v-row>
<v-col>
<v-text-field :rules="Rules" v-model="item.product_url" label="Product URL"></v-text-field>
</v-col>
</v-row>



</v-container>
<v-container>
<!-- <v-row>
<v-col>
<v-btn class="primary black--text" @click="i++">Add More <v-icon>mdi-plus</v-icon></v-btn>
</v-col>
</v-row> -->
</v-container>

</v-form>

</v-card-text>



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
</v-card>
</v-app>
</template>


<script>

export default {
async created () {
     this.collection_id = this.$route.params.album_id;

     let colors = await this.$axios.get('colors');

     this.colors = colors.data.map((v) => ({
        id  : v.id,
        color  : v.color_name,
        color_code  : v.color_code

     }));

},
data:() => ({
loading:false,  
collection_id : '',
i:1,  
categories:[],
item : {
files:[],   
color_id:[],
product_code:[],
product_title:[],
product_price:[],
product_image:[],
product_description:[],
product_url:[],
},
arr:[],

colors:[],

msg:"",

snackbar:false,

Rules : [
v => !!v || 'This field is required',
],

}),

methods:{

check_attachment(e) { 
  if(!e.length){
    return false;
  }
    for( var i = 0; i < e.length; i++ ){
          this.item.product_image.push( e[i] ); 
    }


},

save(e){


const config = { headers: { 'Content-Type': 'multipart/form-data' } };


let index = this.i - 1;
let product = new FormData();  


let auto_fill = false;

product.append('collection_id',this.collection_id);
product.append('color_id',!auto_fill ?  this.item.color_id : 1);
product.append('product_code',!auto_fill ? this.item.product_code : '789');
product.append('product_title',!auto_fill ? this.item.product_title : 'abc'); 
product.append('product_price',!auto_fill ? this.item.product_price : 500);
product.append('product_description',!auto_fill ? this.item.product_description : 'test');
product.append('product_url',!auto_fill ? this.item.product_url : 'test');


  for(var j = 0; j < this.item.product_image.length; j++){
     product.append('product_images['+j+']', this.item.product_image[j]);  
  }  

if(this.$refs.form.validate()){

  this.$axios.post('product',product,config).then((res,status) => {
        this.loading = true;  
        setTimeout(() => {
            this.$router.push('product/'+this.collection_id);
            this.loading = false;
        },1000);
    
}).catch(() => this.errors = res.data.errors);
}

},

}

}
</script>