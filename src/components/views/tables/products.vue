<template>
  <div>
    <div class="layout-padding">
      <div class="card">
        <div class="card-title bg-teal text-white"></div>
        <div class="card-content">
          <div class="flex wrap gutter">
            <div class="auto">
              <q-search placeholder="Search for product name" :debounce="500"
                        v-model.lazy="fetchDataByName" @input="fetchData"/>
            </div>
            <!-- <div class="auto">
              <q-pagination
                v-model="page"
                :max="10"
              ></q-pagination>
            </div> -->
          </div>
          <q-data-table
            :data="rows"
            :config="configs"
            :columns="columns">

            <template slot="col-image_url" scope="cell">
              <tooltip-button :url="cell.row.image_url"></tooltip-button>
            </template>
          </q-data-table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import tooltipButton from './tooltipButton.vue'
  import axios from 'axios'
  export default {
    mounted () {
      this.fetchData()
    },
    data () {
      return {
        rows: [],
        page: 1,
        fetchDataByName: '',
        columns: [
          { label: 'Title', field: 'title', width: '80px', sort: true },
          { label: 'Description', field: 'description', width: '150px', sort: true },
          { label: 'Product Type', field: 'product_type', width: '50px', sort: true },
          { label: 'Picture', field: 'image_url', width: '50px' }
        ],
        configs: {
          columnPicker: true,
          title: 'Product List'
        }
      }
    },
    watch: {
      page () {
        this.fetchData()
      }
    },
    computed: {
      url () {
        return `beers?page=${this.page}&per_page=10${this.search}`
      },
      search () {
        return this.fetchDataByName ? `&beer_name=${this.fetchDataByName}` : ''
      }
    },
    methods: {
      fetchData () {
        axios.get('http://localhost/api/shopify/product').then((response) => {
          const { data } = response;
          let filterData = data.map((v) => {
            return {
              'title' : v.title,
              'description' : v.body_html,
              'image_url' : v.image ? v.image.src : 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcS9Z4AjvBVreDfdnnudymxgnDB3AyEUKA9BrM2b1MtaBXBPAvzK5w&s',
              'product_type' : v.product_type
            }
          })
          this.rows = filterData;
        })
      }
    },
    components: {
      tooltipButton
    }
  }
</script>

<style scoped>
  .grid-filter{
    max-width: 200px
  }
  .grid-search{
    max-width: calc(100% - 400px)
  }
  .grid-pagination{
    max-width: 200px
  }
</style>
