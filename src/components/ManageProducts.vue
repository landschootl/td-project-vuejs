<template>
  <div class="hello">
    <h1>La liste de mes produits :</h1>
    <div v-for="(product, index) in products" :key="index">
      <span>{{product.name}} - </span><span>{{product.price}} €</span>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'ManageProducts',
  data() {
    return {
      backendUrl: 'https://node-baseapi.herokuapp.com/api',
      products: []
    }
  },
  methods: {
    getAllProducts() {
      axios.get(`${this.backendUrl}/products`)
              .then(response => {
                this.products = response.data.result;
              })
              .catch(() => {
                alert('impossible de récupérer les produits');
              });
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
