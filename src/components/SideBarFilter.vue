<template>
  <div class="sidebar-filter">
    <div class="buttons">

    <button class="button is-success" id="clrallbutton" @click="clearFilters">clear all</button>
    </div>
    <div class="filter-category">
      <h3>Category</h3>
      <label v-for="item in categories" :key="item">
        <input type="radio" v-model="selectedCategory" :value="item" />
        {{ item }}
      </label>
    </div>



    <div class="filter-category">
      <h3>Price Range</h3>
      <label v-for="item in priceRanges" :key="item">
        <input type="radio" v-model="selectedPriceRange" :value="item" />
        {{ item }}
      </label>
    </div>


  </div>
</template>

<script>
export default {
  data() {
    return {
      categories: ['Утас', 'Компюьтер', 'Камер', 'Speaker'],
      priceRanges: ['< $500', '$500 - $1000', '$1000 - $1500', '> $1500'],
      selectedCategory: '',
      selectedBrand: '',
      selectedRam: '',
      selectedPriceRange: '',
      selectedType: ''
    };
  },
  methods: {
    clearFilters() {
      this.selectedCategory = '';
      this.selectedBrand = '';
      this.selectedRam = '';
      this.selectedPriceRange = '';
      this.selectedType = '';
    }
  }
};
</script>

<style scoped>
.sidebar-filter {
  padding: 20px;
  background-color: #f8f8f8;
  border: 1px solid #ddd;
  width: 200px;
}

.sidebar-filter button {
  display: block;
  margin-bottom: 20px;
  background-color: #f5f5f5;
  border: none;
  padding: 10px;
  cursor: pointer;
}

.filter-category {
  margin-bottom: 20px;
}

.filter-category h3 {
  margin-bottom: 10px;
}

.filter-category label {
  display: block;
  margin-bottom: 5px;
  cursor: pointer;
}

.filter-category input[type="radio"] {
  margin-right: 10px;
}

#clrallbutton{
  background-color: #a3bbc8;
  color: white;
}
</style>
