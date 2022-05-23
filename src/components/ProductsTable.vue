<template>
  <div>
    <v-btn
      class="btn-download"
      @click="downloadFile"
    >
      Download
    </v-btn>
    <v-simple-table>
      <template v-slot:default>
        <thead>
        <tr>
          <th class="text-left">
            ID
          </th>
          <th class="text-left">
            Name
          </th>
          <th class="text-left">
            Price
          </th>
          <th class="text-left">
            Is package
          </th>
        </tr>
        </thead>
        <tbody>
        <tr
            v-for="product in products"
            :key="product.id"
        >
          <td>{{ product.id }}</td>
          <td>{{ product.name }}</td>
          <td>{{ product.price }}</td>
          <td>{{ product.isPackage }}</td>
        </tr>
        </tbody>
      </template>
    </v-simple-table>
  </div>
</template>

<script>
export default {
  name: "ProductsTable",
  data: () => ({
    products: [],
    currentIndex: 0,
  }),
  async mounted () {
    await fetch('https://raw.githubusercontent.com/OlegFomishyn/test-data/main/products.json').then(res => res.json()).then(data => this.products = data);
    this.flatProducts(this.products);
    this.products = this.uniqueArray(this.products);
  },
  methods: {
    flatProducts (arr) {
      arr.map((item, index) => {
        if ('included' in item) {
          this.currentIndex += (index + 1);
          this.products.splice(this.currentIndex, 0, ...item.included);
          item.isPackage = 'Yes';
          this.flatProducts(item.included)
        } else {
          item.isPackage = 'No';
        }
        item.price = '$' + Number(item.price) / 100;
      })
    },
    uniqueArray (a) {
      return [...new Set(a.map(o => JSON.stringify(o)))].map(s => JSON.parse(s));
    },
    downloadFile () {
      let date = new Date();
      date = date.getDate() + '_' + date.getMonth() + '_' + date.getFullYear();
      this.products.map((product) => {
        delete product.included;
        delete product.isPackage;
        return product;
      })
      const data = JSON.stringify(this.products);
      const blob = new Blob([data], {type: 'text/plain'});
      let a = document.createElement('a');
      a.download = `${date}.json`;
      a.href = window.URL.createObjectURL(blob);
      a.dataset.downloadurl = ['text/json', a.download, a.href].join(':');
      a.click();
    }
  }
}
</script>

<style scoped>
  .btn-download {
    margin: 20px;
  }
</style>
