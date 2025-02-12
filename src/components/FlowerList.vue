<script setup>
  import { ref, computed } from 'vue'
  import FlowerCard from './FlowerCard.vue'
  import Sorting from './Sorting.vue'

  const {
    flowers,
    filteredFlowers,
    searchQueryExists,
    sortCategory,
    sortMonth,
    extractMonth
  } = defineProps({
    flowers: Array,
    filteredFlowers: Array,
    searchQueryExists: Boolean,
    sortOrder: String,
    sortMonth: String,
    sortCategory: String,
    extractMonth: Function
  })

  const groupedByCategory = computed(() => {
    if (sortCategory !== 'category') return null

    return flowers.reduce((categories, flower) => {
      if (!categories[flower.category]) {
        categories[flower.category] = []
      }
      categories[flower.category].push(flower)
      return categories
    }, {})
  })

  const groupedByMonth = computed(() => {
    if (sortMonth !== 'asc') return null

    return flowers.reduce((months, flower) => {
      const month = extractMonth(flower.sowingDate)
      if (!months[month]) {
        months[month] = []
      }
      months[month].push(flower)
      return months
    }, {})
  })

  const sortedFlowers = computed(() => {
    if (sortCategory === 'category') return groupedByCategory.value
    if (sortMonth === 'asc') return groupedByMonth.value
    return filteredFlowers
  })

  const getMonth = (month) => {
    const monthMap = {
      1: 'Januari',
      2: 'Februari',
      3: 'Mars',
      4: 'April',
      5: 'Maj',
      6: 'Juni',
      7: 'Juli',
      8: 'Augusti',
      9: 'September',
      10: 'Oktober',
      11: 'November',
      12: 'December'
    }
    return monthMap[month] || '???'
  }
</script>

<template>
  <div v-if="sortedFlowers" class="cards-container" id="cards-container">
    <Sorting
      :sortOrder="sortOrder"
      :sortCategory="sortCategory"
      :sortMonth="sortMonth"
    />
    <template v-if="sortCategory === 'category' || sortMonth === 'asc'">
      <div
        class="cards-by-sort"
        v-for="(flowers, key) in sortedFlowers"
        :key="key"
      >
        <h1>{{ sortCategory === 'category' ? key : getMonth(key) }}</h1>
        <hr />
        <div class="card-container">
          <FlowerCard
            v-for="flower in flowers"
            :key="flower.id"
            :flower="flower"
          />
        </div>
      </div>
    </template>
    <template v-else>
      <div class="product-cards">
        <FlowerCard
          v-for="flower in sortedFlowers"
          :key="flower.id"
          :flower="flower"
        />
      </div>
    </template>
  </div>
</template>

<style lang="css">
  section {
    width: 100%;
  }

  .cards-container {
    width: 70%;
    height: 100vh;
    margin-bottom: 2rem;
  }

  /* SPECIFIKA STILAR FÖR PRODUCT-CARDS */
  .product-cards {
    display: flex;
    flex-wrap: wrap;
    gap: 1.5rem;
  }

  .product-cards .card {
    max-width: 150px;
  }

  .product-cards .image-wrapper {
    width: 150px;
    height: 150px;
  }

  /* SPECIFIKA STILAR FÖR PRODUCT-CARDS-SORTED */
  .product-cards-sorted {
    display: flex;
    flex-direction: column;
    gap: 1.5rem;
  }

  .cards-by-sort {
    display: flex;
    flex-direction: column;
  }

  .card-container {
    display: flex;
    flex-wrap: wrap;
    gap: 1.5rem;
  }

  .card-container .card {
    max-width: 120px;
  }

  .card-container .image-wrapper {
    width: 120px;
    height: 120px;
  }

  .cards-by-sort h1 {
    font-size: 2rem;
    margin-bottom: 0;
  }

  .cards-by-sort hr {
    border-top: 2px solid var(--color-dark-grey);
    box-shadow: 0 0px 6px rgba(0, 0, 0, 0.3);
    margin: 0 0 1rem 0;
  }
</style>
