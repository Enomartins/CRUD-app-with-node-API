<template>
  <div class="products">
    <h3>Products</h3>
    <div class="card">
      <div class="card-header">
        Add a new product
      </div>
      <div class="card-body">
        <form class="form-inline" v-on:submit.prevent="onSubmit">
          <div class="form-group">
            <label>Category</label>
            <input v-model="formData.category" type="text" class="form-control ml-sm-2 mr-sm-4 my-2"  required>
          </div>
          <div class="form-group">
            <label>ItemName</label>
            <input v-model="formData.item" type="text" class="form-control ml-sm-2 mr-sm-4 my-2" required>
          </div>
          <div class="form-group">
            <label>Price</label>
            <input v-model="formData.price" type="text" class="form-control ml-sm-2 mr-sm-4 my-2" required>
          </div>
          <div class="ml-auto text-right">
            <button type="submit" class="btn btn-primary my-2">Add</button>
            
          </div>
        </form>
      </div>
    </div>

    <div class="card mt-5">
      <div class="card-header">
        Product List
      </div>
      <div class="card-body">
        <div class="table-responsive">
          <table class="table">
            <thead>
              <tr>
                <th scope="col">
                  Product ID
                </th>
                <th>
                  Product Name
                </th>
                <th>
                  Product Price
                </th>
                <th>
                  Action
                </th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="product in productData" v-bind:key="product._id">
                <template v-if="editId == product._id">
                  <td><input v-model="editProductData.category" type="text"></td>
                  <td><input v-model="editProductData.item" type="text"></td>
                  <td><input v-model="editProductData.price" type="text"></td>
                  <td>
                    <span class="icon">
                      <i  @click="onEditSubmit(product._id)" class="fa fa-check"></i>
                    </span>
                    <span class="icon">
                      <i  @click="onCancel" class="fa fa-ban"></i>
                    </span>
                  </td>
                </template>
                <template v-else>
                  <td>
                    {{product.category}}
                  </td>
                  <td>
                    {{product.item}}
                  </td>
                  <td>
                    {{product.price}}
                  </td>
                  <td>

                    <a href="#" class="icon">
                      <i v-on:click="onDelete(product._id)" class="fa fa-trash"></i>
                    </a>
                    <a href="#" class="icon">
                      <i v-on:click="onEdit(product)" class="fa fa-pencil"></i>
                    </a>
                    <router-link 
                    :to="{
                      name:'ProductPage', 
                      params:{id: product._id}
                    }" 
                    class="icon"
                    >
                      <i class="fa fa-eye"></i>
                    </router-link>
                  </td>
                </template>
              </tr>

            </tbody>
          </table>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'Products',
  data () {
    return {
      editId: '',
      formData: {},
      productData: {},
      editProductData: {
        'id' : '',
        'product_id': '',
        'product_name': '',
        'product_price': ''
      },
      products: []
    }
  },
  created() {
    this.getProducts()
  },
  computed:{
    sortedProducts(){
      return this.products.slice().sort((a,b)=>{
        return a.product_id - b.product_id
      })
    }
  },
  methods: {
    getProducts() {

      axios.get('https://itemsdiaryapi.herokuapp.com/api/items')
          .then((response) => {

          console.log(response.data);
          this.productData = response.data;
      })
    },
    onSubmit(){
      axios.post('https://itemsdiaryapi.herokuapp.com/api/items', {
        ...this.formData
      })
      .then((response) => {
        this.formData = {};
        this.getProducts()
      }, (error) => {
        console.log(error);
      });

    },
    // onDelete(product_id){
    //   db.collection('products').where('product_id', '==', product_id).get().then(querySnapshot =>{
    //     querySnapshot.forEach(doc=>{
    //       doc.ref.delete().then(this.getProducts)
    //     })
    //   })
    // }
    onDelete(id){
      axios.delete(`https://itemsdiaryapi.herokuapp.com/api/items/${id}`)
      .then((response) => {
        console.log("deleted")
        this.getProducts()
      }, (error) => {
        console.log(error);
      });

      
    },
    onEdit(product){
      this.editId = product._id
      this.editProductData.category = product.category
      this.editProductData.item = product.item
      this.editProductData.price = product.price
    },
    onCancel(){
      this.editId = ''
      this.editProductData.category = ''
      this.editProductData.item = ''
      this.editProductData.price = ''
    },
    onEditSubmit (id){
      axios.put(`https://itemsdiaryapi.herokuapp.com/api/items/${id}`, {...this.editProductData})
      .then((response) => {
        console.log("updated")
        this.getProducts()
      }, (error) => {
        console.log(error);
      });

        this.editId = ''
        this.editProductData.category = ''
        this.editProductData.item = ''
        this.editProductData.price = ''
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3{
  text-align: center;
  margin-top: 30px;
  margin-bottom: 20px;
}
.icon{
  margin-right: 10px;
}
.icon i{
  cursor: pointer;
}
</style>
