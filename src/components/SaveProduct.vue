<template>
  <div id="save-product">
    <h1>{{isCreateMode ? 'Créer un nouveau produit' : 'Modifier le produit'}}</h1>
    <form @submit.prevent="submitProduct()">
      <input type="text" v-model="product.name"/>
      <input type="text" v-model.number="product.price"/>
      <button type="submit">Sauvegarder le produit</button>
    </form>
  </div>
</template>

<script>
  export default {
  name: 'SaveProduct',
  props: {
    isCreateMode: Boolean,
    product: Object
  },
  methods: {
    submitProduct() {
      this.$emit('saveProduct', this.product);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
