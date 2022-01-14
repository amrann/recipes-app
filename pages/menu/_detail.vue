<template>
  <div class="home">
    <Navbar />
    <div class="container">
      <div class="row mt-4">
        <div class="col">
          <nav aria-label="breadcrumb">
            <ol class="breadcrumb">
              <li class="breadcrumb-item">
                <div class="text-dark">Menu</div>
              </li>
              <li class="breadcrumb-item active" aria-current="page">Detail</li>
            </ol>
          </nav>
        </div>
      </div>

      <Loading v-if="$fetchState.pending" />

      <div v-else class="row mt-3">
        <div class="col-md-8">
          <h2>
            <strong>{{ item.title }}</strong>
          </h2>
          <hr />
          <div class="row">
            <div class="col-md-2" >
              <label><strong>Summary : </strong></label>
            </div>
            <div class="col" >
              <label v-html="item.summary" />
            </div>
          </div>
          <div class="row">
            <div class="col-sm-3" >
              <label><strong>Nutritions : </strong></label>
            </div>
            <div class="col" >
              <ul>
                <li v-for="(x, i) in dt_nutrition" :key="i">
                  {{ x.name }} - {{x.amount}}{{x.unit}}
                </li>
              </ul>
              <!-- <label v-for="(x, i) in dt_nutrition" :key="i"> {{ x.name }} ({{x.amount}}{{x.unit}}),&nbsp; </label> -->
            </div>
          </div>
        </div>
        <div class="col-md-4 card-product">
          <img :src="item.image" class="card-img-top" alt="..." />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data () {
    return {
      item: {},
      dt_nutrition: []
    }
  },
  head() {
    return {
      title: this.item.title,
    }
  },
  async fetch() {
    await this.getItem()
  },
  fetchDelay: 1000,
  methods: {
    async getItem () {
      const data = axios.get(
          `https://api.spoonacular.com/recipes/${this.$route.params.id}/information?includeNutrition=true&apiKey=119a80cd7b794618ba6b41c48a1c83d8`
        )
        const results = await data
        this.item = results.data
        console.log(this.item, 'item detail')
        for (let i = 0; i < this.item.nutrition.nutrients.length; i++) {
          this.dt_nutrition.push(this.item.nutrition.nutrients[i])
        }

    }
  }
}
</script>