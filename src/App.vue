<template>
  <v-app>
    <v-main>
      <ProductsTable />
    </v-main>
  </v-app>
</template>

<script>
import ProductsTable from '@/components/ProductsTable';

export default {
  name: 'App',

  components: {
    ProductsTable,
  },

  data: () => ({
    //
  }),
};
</script>
