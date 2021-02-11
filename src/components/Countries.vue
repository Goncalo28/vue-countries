<template>
  <div class="countries">
    <form class="form">
      <input type="search" 
      v-model="search" 
      @focus="filterCountries"/>
    </form>
    <div v-if="filteredCountries">
      <ul class="countries-list">
        <li v-for="country in filteredCountries" 
        :key="country" 
        @click="setCountry(country)">
          {{country}}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      search: '',
      countries: [],
      filteredCountries: []
    }
  },

  async mounted() {
    const fetchedData = await axios.get('https://restcountries.eu/rest/v2/all');
    const countries = fetchedData.data.map(country => country.name);
    this.countries = countries;
  },

  methods: {
    filterCountries() {
      this.filteredCountries = this.countries.filter((country) => {
        return country.toLowerCase().includes(this.search.toLowerCase())
      });
    },

    setCountry(search) {
      this.search = search;
    }
  },

  watch: {
    search() {
      this.filterCountries()
    }
  }
}
</script>

<style scoped>
.countries {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.form {
  width: 350px;
}

ul {
  padding-left: 0px;
}

li {
  padding-left: 0px;
  list-style-type: none;
  width: 100%;
  margin-bottom: 5px;
}

li:hover {
  color: rgb(45, 214, 53);
  cursor: pointer;
}

input {
  width: 100%;
  height: 25px;
}
</style>
