<script setup>
useHead({
  title: 'Kasta om bokstäver',
})


const client = useSupabaseClient()

const { data: coutries } = await useAsyncData('countries', async () => {
  const { data, error } = await client.from('countries').select('* ')
  if (error) {
    console.log(error);
  }
  return data
})

// console.log(coutries.value);

const searchString = ref('')

const suggestedCoutries = computed(() => {
  const searchArrey = searchString.value.split('')

  let countryArray = []

  countryArray = coutries.value.filter((str) => {
    return searchArrey.every(letter => str.Land.toLowerCase().includes(letter.toLowerCase()));
  });

  countryArray = countryArray.filter((str) => {
    return str.Land.replace(/\s/g, '').length === searchString.value.replace(/\s/g, '').length
  })

  return countryArray

})


</script>

<template>
  <div class="page">
    <h1>Kasta om bokstäver till länder räknare</h1>
    <div class="side-by-side">
      <input type="text" placeholder="Omkastade bokstäver" v-model="searchString" :class="{ 'outlined': searchString }">
      <p class="arrow hide-on-phone">→</p>
      <div class="result" v-for="country in suggestedCoutries">{{ country.Land }}</div>
    </div>
  </div>
</template>

<style>
* {
  font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  box-sizing: border-box;
}

body {
  height: 100vh;
  margin: 0;
  width: 100vw;
}

.page {
  display: grid;
  place-items: center;
  height: 100%;
  width: 100%;
  padding: 10rem 1rem;
}

html {
  background: #161616;
  color: white;
}

input,
.result {
  background: none;
  border: 2px solid #282828;
  color: white;
  font-size: 1.5rem;
  text-align: center;
  border-radius: 10rem;
  padding: 0.5rem;
  margin-bottom: 0.5rem;
}

input:focus,
.outlined {
  border-color: #87c232;
  outline: none;
}

h1 {
  font-size: 2rem;
  text-align: center;
}

.side-by-side {
  display: grid;
  grid-template-columns: 1fr;
}

@media screen and (min-width: 700px) {
  .side-by-side {
    display: grid;
    grid-template-columns: 1fr auto 1fr;
  }
}

.arrow {
  font-size: 2rem;
  margin: auto 0.5rem;
  text-align: center;
}

@media screen and (max-width: 700px) {
  .hide-on-phone {
    display: none;
  }
}
</style>
