<template>
  <div class="countries">
    <form class="form">
      <input type="search" 
      v-model="search" 
      @focus="filterCountries"
      @keyup="isLoading = !isLoading"
    />
    </form>
    <h2 v-if="isLoading">Loading...</h2>
    <h2 v-if="filteredCountries.length === 0 && search.length !== 0 && !isLoading">No results found</h2>
    <div v-if="filteredCountries && !isLoading">
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
import _ from 'lodash';
import {ref, onMounted, watch} from 'vue';

export default {

  // Vue 3
  setup() {
    const search = ref('');
    const countries = ref([]);
    const filteredCountries = ref([]);
    const isLoading = ref(false);

    onMounted(async() => {
      try {
        const fetchedData = await axios.get('https://restcountries.eu/rest/v2/all');
        const countriesData = fetchedData.data.map(country => country.name);
        countries.value = countriesData;
      } catch (error) {
        throw new Error('An error ocurred', error);
      }
    })

    const filterCountries = _.debounce( function() {
      filteredCountries.value = countries.value.filter((country) => {
        return country.toLowerCase().includes(search.value.toLowerCase());
      });
      isLoading.value = false; 
    }, 500)
  
    const setCountry = (selectedCountry) => {
      search.value = selectedCountry;
    }

    watch(search, () => {
      filterCountries();
    })

    return {
      search,
      filteredCountries,
      isLoading,
      filterCountries,
      setCountry
    }
  }

  // Vue 2 
  // data() {
  //   return {
  //     search: '',
  //     countries: [],
  //     filteredCountries: [],
  //     isLoading: false
  //   }
  // },

  // async mounted() {
  //   try {
  //     const fetchedData = await axios.get('https://restcountries.eu/rest/v2/all');
  //     const countries = fetchedData.data.map(country => country.name);
  //     this.countries = countries;
  //   } catch (error) {
  //     throw new Error('An error ocurred', error);
  //   }
  // },

  // methods: {
  //   filterCountries: _.debounce( function() {
  //     this.filteredCountries = this.countries.filter((country) => {
  //       return country.toLowerCase().includes(this.search.toLowerCase())
  //     });
  //     this.isLoading = false; 
  //   }, 500),
  
  //   setCountry(selectedCountry) {
  //     this.search = selectedCountry;
  //   },
  // },

  // watch: {
  //   search() {
  //     this.filterCountries()
  //   }
  // }
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
