<template>
  <div class="column is-full-mobile is-half-tablet is-one-third-desktop is-one-quarter-widescreen">
    <div class="card">
      <header class="card-header">
        <p class="card-header-title">{{ product.attributes.name }}</p>
      </header>
      <div class="card-image" @click="isModalActive=true">
        <figure class="image">
          <img :src="'http://localhost:1337' + product.attributes.media.data[0].attributes.url">
        </figure>
      </div>
      <footer class="card-footer">
        <!--
        <div style="with: 100%; height: 100px !important; overflow-y: scroll;">
            {{ product.attributes.Description }}
        </div>
        -->
        <ul v-for="location in product.attributes.locations.data">
          <li>{{ location.attributes.code }}</li>
        </ul>
      </footer>

    </div>
  </div>
</template>

<script>
export default {
  props: {
    product: {
      type: Object
    }
  },
  data() {
    return {
      isModalActive: false
    };
  },
};
</script>

<style>
.card-image,
.modal-background {
  cursor: pointer !important;
}
.card footer li {
  padding: 5px;
}
</style>
