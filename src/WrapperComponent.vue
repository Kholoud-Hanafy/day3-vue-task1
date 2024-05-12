<template>

<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <div class="container-fluid">
    
    <div class="collapse navbar-collapse" id="navbarNav">
      <ul class="navbar-nav mx-auto w-70">
        <li class="nav-item mx-3">
          <a class="nav-link active" aria-current="page" href="#" @click.prevent=" targetComponent ='ProductComponent'">Products</a>
        </li>
        <li class="nav-item  mx-3">
          <a class="nav-link" href="#"  @click.prevent=" targetComponent ='BooksComponent'" >Books</a>
        </li>
      </ul>      
    </div>
    
  </div>
</nav>
    <div> 
         <keep-alive>
        <component :is="targetComponent"/>
         </keep-alive>
        </div>
</template>

<script>
import 'bootstrap/dist/css/bootstrap.css'
import BooksComponent from '@/components/BooksComponent.vue'
import ProductComponent from '@/components/ProductComponent.vue'

export default {
    data: ()=>({

        targetComponent : 'ProductComponent'
    }),
  components : {
    BooksComponent, ProductComponent
  }
}
</script>

<style>

</style>