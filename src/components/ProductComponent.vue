<template>
  <div>
    <div class="nav p-3">
      <a href="#" class="logo mx-5" @click.prevent="isListVisible=false">Product Store</a>
      <button class="btn1 mx-5" @click="isListVisible=true">Cart</button>
    </div>
   <div class="container">
  <button type="button" class="btn4  w-100" data-bs-toggle="modal" data-bs-target="#addModel" v-if="!isListVisible" >Add Product</button>
  </div>
    <!-- Start card -->
    <div class="container">
      <div class="row" v-if="!isListVisible">
        <div class="col-lg-4" v-for="product in products" :key="product.id">
          <div class="card my-5 mx-5">
            <img :src="product.image" class="card-img-top" :alt="product.name">
            <div class="card-body">
              <h5 class="card-title">{{ product.name }}</h5>
              <p class="card-text"><b>Price:</b> {{ formatCurrency(product.price) }}</p>
              <p class="card-text description ">{{ product.description }}</p>
              
                <p class="card-text">
                    <b  :class="{
                        'more': product.instock > 10,
                        'less': product.instock < 10 && product.instock > 0,
                        'none': product.instock === 0
                    }">In Stock:</b>
                    <span >{{ product.instock }}</span>
                    </p>


              <button :class="['btn2 mt-2 w-100', isProductInCart(product) ? 'added' : '']" @click="addToCart(product)">
                Add To Cart
              </button>
               <div   class="btns   my-3 ">
                <button class="btn btn-success mx-2 "  data-bs-toggle="modal" data-bs-target="#updateModel" @click="fillData(product)">Update</button>
                <button class="btn btn-danger mx-2"   @click="deleteProduct(product)">Delete</button>
               </div>

            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- End card -->

    <!-- Start cart -->
    <div v-if="isListVisible">
      <h2 class="mt-4" v-if="cart.length > 0">Cart</h2>

      <table class="table" v-if="cart.length > 0">
        <thead>
          <tr>
            <th>Name</th>
            <th>Price</th>
            <th>Quantity</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in cart" :key="item.id">
            <td>{{ item.name }}</td>
            <td>{{ formatCurrency(item.price) }}</td>
            <td>{{ item.quantity }}</td>
            <td><button class="btn btn-danger" @click="removeFromCart(index, item)">Remove</button></td>
          </tr>
        </tbody>
      </table>
      <h3 v-else>No items in your cart</h3>
    </div>
    <!-- End cart -->


    <!-- modal dialog for adding  -->
 <div class="modal" id="addModel" data-bs-backdrop= "static" data-bs-keyboard="false">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title ">Add Product</h5>
      </div>
      <div class="modal-body">
        <input type="text" class="form-control my-2 w-100" placeholder="Name" v-model="newProduct.name"  required >
       <input type="number" class="form-control my-2 w-100 " id="price" placeholder="price"    v-model="newProduct.price" required>
       <textarea class="form-control my-2 w-100" id="description"   placeholder="description"  v-model="newProduct.description" required></textarea>
        <input type="number" class="form-control  my-2 w-100 " id="instock"  placeholder="inStoke" v-model="newProduct.instock" required>
         <input type="text" class="form-control  my-2 w-100 " id="image"   placeholder="image" v-model="newProduct.image" required>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-primary" data-bs-dismiss="modal" @click="addNewProduct(newProduct)">OK</button>
        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancel</button>
      </div>
    </div>
  </div>
</div>
  </div>

 <!-- modal dialog for updateing  -->
 <div class="modal" id="updateModel" data-bs-backdrop= "static" data-bs-keyboard="false">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title ">Update Book</h5>
      </div>
     
          <div class="modal-body">
             <input type="text" class="form-control my-2 w-100" placeholder="id" v-model="editedProduct.id"  disabled >
        <input type="text" class="form-control my-2 w-100" placeholder="Name" v-model="editedProduct.name"  required >
       <input type="number" class="form-control my-2 w-100 " id="price" placeholder="price"    v-model="editedProduct.price" required>
       <textarea class="form-control my-2 w-100" id="description"   placeholder="description"  v-model="editedProduct.description" required></textarea>
        <input type="number" class="form-control  my-2 w-100 " id="instock"  placeholder="inStoke" v-model="editedProduct.instock" required>
         <input type="text" class="form-control  my-2 w-100 " id="image"   placeholder="image" v-model="editedProduct.image" required>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-primary" data-bs-dismiss="modal"  @click="updateProduct(editedProduct)">Update</button>
        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancel</button>
      </div>
    </div>
  </div>
</div>

</template>

<script>
export default {


  data() {
    return {
      products: [],
      isListVisible: false,
      cart: [],
      newProduct: {
        name: '',
        price: 0,
        description: '',
        instock: 0,
        image: ''
      },
      editedProduct: {
        id: '',
        name: '',
        price: 0,
        description: '',
        instock: 0,
        image: ''
      }

    };
  },
  created() {
    this.fetchProducts();
  },
  methods: {
    async fetchProducts() {
      try {
        const response = await fetch('http://localhost:5000/products');
        this.products = await response.json();
        
      } catch (error) {
        console.error('Error fetching products:', error);
      }
    },
    formatCurrency(value) {
      return new Intl.NumberFormat('ar-eg', {
        style: 'currency',
        currency: 'EGP',
        minimumFractionDigits: 0,
      }).format(value);
    },
    addToCart(product) {
      const existingItem = this.cart.find(item => item.id === product.id);
      if (existingItem) {
        
        if(product.instock !==0){
           existingItem.quantity++; 
            product.instock--;
        }

         
      } else {
        this.cart.push({ ...product, quantity: 1 });
        product.instock--;
      }
    },
    removeFromCart(index) {
      this.cart.splice(index, 1);
      
    },
    isProductInCart(product) {
      return this.cart.some(item => item.id === product.id);
    },
    
    //add product
    async addNewProduct(newProduct){
         const lastProductId = this.products.length > 0 ? Number(this.products[this.products.length - 1].id)+100: 0;
        
        // Assign the id for the new product
        newProduct.id = lastProductId.toString();

        fetch("http://localhost:5000/products",{
            method : "POST",
              headers: {
            "content-type": "application/json"
        },
        body: JSON.stringify(newProduct )
        
        })

       this.products.push(newProduct)
        
        this.newProduct= {
        name: '',
        price: 0,
        description: '',
        instock: 0,
        image: ''
      }
        
        
    },
    ///delet product
     async deleteProduct(product){
      let res =  await fetch(`http://localhost:5000/products/${product.id}`,
            {
                method: "DELETE"
            }
        )

      if (!res.ok) {
      throw new Error(`Failed to delete product with ID :  ${product.id}.`);
    }

    // Update the products array by filtering out the deleted product
     this.products = this.products.filter(prd=>prd.id !== product.id)
        alert("product deleted succesfuly")  
    },

    ///update product

    fillData(product){
        this.editedProduct=product
    },

    //
  async updateProduct(editedProduct){
          await fetch(`http://localhost:5000/products/${editedProduct.id}`,{
                method : "PUT",
              headers: {
            "content-type": "application/json"
        },
        body: JSON.stringify(editedProduct )
          })
          
          const index = this.products.findIndex(prd=>prd.id === editedProduct.id);
    
        if (index !== -1) {
            // Update the book in the local array
            this.products[index] = { ...this.editedProduct};
        }


  }


  },
};
</script>

<style scoped>

 .nav{
        display: flex;
        align-items: baseline;
        justify-content: space-between;
        /* background-color: #070722; */
    }
    .nav .logo{
      text-decoration: none;
      background: linear-gradient(to right, #c22292, rgb(31, 31, 207));
  
  /* Apply the gradient to the text */
  -webkit-background-clip: text;
  background-clip: text;
  
  /* Set text color to transparent */
  color: transparent;
  
  font-size: 25px !important;
    }

    .nav .logo:hover{
    background: linear-gradient(to right,rgb(31, 31, 207), rgb(194, 34, 146) );
  
    /* Apply the gradient to the text */
    -webkit-background-clip: text;
    background-clip: text;
    
    /* Set text color to transparent */
    color: transparent;
}


.btn1{
    background-color: #e432ae;
    border: 2px solid #e432ae !important;
    color: white ;
    padding: 10px 30px;
    border-radius: 10px;

}
.btn2{
  background-color: #070722;
    border: 2px solid #070722 !important;
    color: white ;
    /* padding: 4px ; */
    /* margin:0rem 4rem; */
    border-radius: 10px;
    align-items: center;
    

}
.more{
  color:#c22292
}
.less{
  color: orangered
}
.none{
  color: rgb(107, 10, 10)
}
.more,.none,.less{
  font-weight: bold;
}
h3, h2{
  text-align: center;
}
.added{
  background-color: #e432ae;
  border: 2px solid #e432ae !important;
}

.description {
  max-height: 1.5em; /* Adjust the maximum height to fit one line */
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.btn4{
     background-color: #070722;
    border: 2px solid #070722 !important;
    padding: 10px;
    color: lightgrey;
    border-radius: 10px;
    font-size: 20px;
    /* font-weight: bold; */
}

.btns {
    display: flex;
    align-items: center;
    justify-content: space-between;
}
</style>
