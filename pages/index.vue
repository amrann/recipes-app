<template>
  <div class="home">
    <Navbar />
    <div class="container">
      <Header />
      <div class="row mt-5 searching">
        <div class="col-md-4">
          <div class="input-group">
            <input
              v-model="cari"
              type="text"
              class="form-control"
              placeholder="Cari Resep ..."
              aria-label="Cari"
              aria-describedby="basic-addon1"
              @keyup.enter="$fetch"
            />
            <div v-show="cari !== ''" class="input-group-prepend">
              <span class="input-group-text" type="submit" id="basic-addon1" @click="clearSearch">
                Clear
              </span>
            </div>
          </div>
        </div>
      </div>

      <Loading v-if="$fetchState.pending" />

      <div id="recipe-grid">
        <div class="row mt-2 mb-4" v-if="paginatedItems.length">
          <div class="col-md-3 mt-4" v-for="item in paginatedItems" :key="item.id">
            <div class="card shadow card-product">
              <img :src="item.image" alt="..." />
              <div class="card-body">
                <h5 class="card-title">{{item.title}}</h5>
                <NuxtLink 
                  class="btn btn-success"
                  :to="{ name: 'menu-detail', params: { id: item.id } }"
                > Lihat Detail </NuxtLink>
              </div>
            </div>
          </div>
        </div>
        <div v-else class="row mt-2 mb-4" >
          <div class="col-md-3 mt-4">
            <h3> Data Tidak Ditemukan</h3>
          </div>
        </div>
        <div class="row">
          <div class="col mt-2 mb-4 pagi">
            <b-pagination
              @change="onPageChanged"
              :total-rows="totalRows"
              :per-page="perPage"
              v-model="currentPage"
              class="my-0"
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'IndexPage',
  head() {
    return {
      title: 'Recipes Apps',
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: 'Try all the recipes food and beverages from restaurant',
        },
        {
          hid: 'keywords',
          name: 'keywords',
          content: 'foods, beverages, recipe',
        },
      ],
    }
  },
  data () {
    return {
      itemLists: [],
      currentPage: 1,
      perPage: 8,
      totalRows: null,
      paginatedItems: [],
      cari: ''
    }
  },
  async fetch() {
    if (this.cari === '') {
      await this.getItem()
      return
    }
    if (this.cari !== '') {
      await this.searchBy()
      return
    }
  },
  fetchDelay: 1000,
  methods: {
    // getItem () {
    //   axios.get(`https://api.spoonacular.com/recipes/complexSearch?query=&number=30&apiKey=119a80cd7b794618ba6b41c48a1c83d8`)
    //     .then((res) => {
    //       this.itemLists = res.data.results
    //       this.paginate(this.perPage, 0)
    //     })
    //     .catch((err) => {
    //       console.log(err)
    //     })
    // },
    async getItem () {
      const data = axios.get(`https://api.spoonacular.com/recipes/complexSearch?query=&number=30&apiKey=119a80cd7b794618ba6b41c48a1c83d8`)
      const results = await data
      this.itemLists = results.data.results
      this.paginate(this.perPage, 0)
    },
    // searchBy () {
    //   axios.get(`https://api.spoonacular.com/recipes/complexSearch?query=${this.cari}&number=30&apiKey=119a80cd7b794618ba6b41c48a1c83d8`)
    //     .then((res) => {
    //       this.itemLists = []
    //       this.itemLists = res.data.results
    //       this.paginate(this.perPage, 0)
    //     })
    //     .catch((err) => {
    //       console.log(err)
    //     })
    // },
    async searchBy () {
      const data = axios.get(`https://api.spoonacular.com/recipes/complexSearch?query=${this.cari}&number=30&apiKey=119a80cd7b794618ba6b41c48a1c83d8`)
      const results = await data
      this.itemLists = []
      this.itemLists = results.data.results
      this.paginate(this.perPage, 0)
    },
    clearSearch() {
      this.cari = ''
      this.itemLists = []
      this.getItem()
    },
    paginate (pageSize, pageNumber) {
      this.paginatedItems = this.itemLists
      this.totalRows = this.itemLists.length
      let itemsToParse = this.itemLists
      this.paginatedItems = itemsToParse.slice(
        pageNumber * pageSize,
        (pageNumber + 1) * pageSize
      )
    },
    onPageChanged (page) {
      this.paginate(this.perPage, page - 1)
    }
  }
}
</script>

<style lang="scss">
  @import "@/assets/home.scss";
</style>
