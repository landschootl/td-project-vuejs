<template>
  <div class="manage-product">
    <h1>La liste de mes produits :</h1>
    <div v-for="(product, index) in products" :key="index">
      <span>{{product.name}} - </span>
      <span>{{product.price}} €</span>
      <span><button @click="updateProduct(product)">Editer le produit</button></span>
    </div>
    <p>
      <button @click="createProduct()">Créer un nouveau produit</button>
    </p>
    <SaveProduct :product="product" :isCreateMode="isCreateMode" @saveProduct="saveProduct($event)"></SaveProduct>
  </div>
</template>

<script>
import axios from 'axios';
import SaveProduct from "./SaveProduct";

export default {
  name: 'ManageProducts',
  components: {
    SaveProduct
  },
  data() {
    return {
      backendUrl: 'https://node-baseapi.herokuapp.com/api',
      products: [],
      product: {name: 'default', price: 1.0}
    }
  },
  methods: {
    getAllProducts() {
      axios.get(`${this.backendUrl}/products`)
              .then(response => {
                this.products = response.data.result;
              })
              .catch(() => {
                alert('Impossible de récupérer les produits');
              });
    },
    updateProduct(product) {
      this.product = Object.assign({}, product);
    },
    createProduct() {
      this.product = {name: 'default', price: 1.0};
    },
    saveProduct(product) {
      if(this.isCreateMode) {
        axios.post(`${this.backendUrl}/products`, product)
                .then(result => {
                  this.products.push(result.data.result);
                  this.product = {name: 'default', price: 1.0};
                })
                .catch(() => {
                  alert('Impossible de créer le produit');
                });
      } else {
        axios.put(`${this.backendUrl}/products/${product.id}`, product)
                .then(() => {
                  this.products = this.products.map(p => {
                    return p.id === product.id ? Object.assign({}, product) : p;
                  });
                  this.product = {name: 'default', price: 1.0};
                })
                .catch(() => {
                  alert('Impossible de modifier le produit');
                });
      }
    }
  },
  computed: {
    isCreateMode() {
      return this.product.id === undefined;
    }
  },
  created() {
    this.getAllProducts();
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
