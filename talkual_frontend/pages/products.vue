<template>
  <section class="section">
    <h2 class="title">Products</h2>
    <div class="columns">
      <div class="column">
        <form action="" v-on:submit.prevent="search">
          <b-input
            type="text"
            id="searchInput"
            v-model="searchText"
            placeholder="Filtro por nombre o código postal"
            required>
          </b-input>
        </form>
      </div>
      <div class="column">
        <a class="button is-primary" @click="search">Buscar</a> &nbsp;
        <a class="button is-info" @click="clear">Limpiar</a>
      </div>
    </div>
    <br>
    <div class="columns is-multiline">
      <Product
        v-for="product in products"
        :key="product.id"
        :product="product"
      />
    </div>
  </section>
</template>

<script>
import Product from '~/components/Product'

export default {
  components: {
    Product
  },
  data() {
    return {
      searchText: ''
    };
  },
  asyncData ({ $axios }) {
    return $axios.get('/products?populate=*')
    .then((res) => {
      console.log(res.data);
      return {
        'products': res.data.data
      }
    })
  },
  methods: {
    search () {
      this.products = []
      this.$el.querySelector('#searchInput').blur()  // esconder teclado móvil

      let url = '/products?populate=*'

      url = url + '&filters[locations][code][$containsi]=' + this.searchText

      return this.$axios.get(url)
      .then((res) => {
        console.log(res.data);
        this.products = res.data.data
      })

    },
    clear () {
      this.searchText = '';
      this.search()
    }
  },
  fetch ({ store, redirect }) {
    if (!store.state.user) {
      return redirect('/')
    }
  }
}
</script>
