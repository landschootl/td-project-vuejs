<template>
  <div class="manage-product">
    <h1>La liste de mes produits :</h1>
    <p>Nombre de produit : {{nbProduct}}</p>
    <p>Prix total des produits : {{totalPrice}} €</p>
    <p>Champ de recherche : <input type="text" v-model="search"/></p>
    <div v-for="(product, index) in productsSearch" :key="index">
      <span>{{product.name}} - </span>
      <span>{{product.price}} €</span>
      <span><button @click="updateProduct(product)">Editer le produit</button></span>
      <span><button @click="deleteProduct(product)">Supprimer le produit</button></span>
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
      product: {name: 'default', price: 1.0},
      search: ''
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
    },
    deleteProduct(product) {
      axios.delete(`${this.backendUrl}/products/${product.id}`)
              .then(() => {
                this.products = this.products.filter(p => p.id !== product.id);
              })
              .catch(() => {
                alert('Impossible de supprimer le produit');
              });
    }
  },
  computed: {
    isCreateMode() {
      return this.product.id === undefined;
    },
    nbProduct() {
      return this.productsSearch.length;
    },
    totalPrice() {
      return this.productsSearch.length > 0
          ? this.productsSearch
            .map(product => product.price)
            .reduce((total, product) => total + product)
          : 0;
    },
    productsSearch() {
      return this.search === ''
              ? this.products
              : this.products
                      .filter(product => product.name.startsWith(this.search));
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
