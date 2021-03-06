<template>
  <main class="resultados container">
    <header class="resultados__header row">
      <h1 class="resultados__header-brand">
        <a href="/">GitSearch</a>
      </h1>
      <search-form class="col-md-8 col-lg-6 offset-md-1 offset-lg-2" />
    </header>
    <section class="resultado" v-show="!isLoading">
      <resultado-count :count="totalResultado"/>

      <ul class="resultado__content row">
        <li v-for="item in resultado" :key="item.id" class="resultado__content-item col-lg-6">
          <resultado :items="item" />
        </li>
      </ul>

      <paginate
        v-model="pageNumber"
        :click-handler="pagination"
        :page-count="34"
        :prev-text="'&#x3c;'"
        :next-text="'&#x3e;'"
        :prev-class="'controlls'"
        :next-class="'controlls'"
        :container-class="'pagination'"
        :page-link-class="'page-link'"
        :page-class="'page-item'"
        v-if="resultado.length > 0" />

    </section>

    <loading v-if="isLoading" />
  </main>
</template>

<script>
import SearchForm from '@/components/home/SearchForm.vue'
import ResultadoCount from '@/components/resultados/ResultadoCount.vue'
import Resultado from '@/components/resultados/Resultado.vue'
import Loading from '@/components/layout/Loading.vue'
import Paginate from 'vuejs-paginate'
import HTTPClient from '@/services'

export default {
  name: 'Resultados',
  components: {
    SearchForm,
    ResultadoCount,
    Resultado,
    Loading,
    Paginate
  },

  data () {
    return {
      totalResultado: '',
      resultado: [],
      pageNumber: parseInt(this.$route.query.page),
      isLoading: false
    }
  },

  mounted () {
    this.getRepositories(this.pageNumber)
  },

  watch: {
    '$route' () {
      this.pageNumber = parseInt(this.$route.query.page)

      this.getRepositories(this.pageNumber)
    }
  },

  methods: {
    async getRepositories (page) {
      try {
        this.isLoading = true

        const query = this.$route.query.q
        const repo = await HTTPClient.get(`search/repositories?q=${query}&page=${page}`)
        const { total_count, items } = repo.data

        this.totalResultado = total_count
        this.resultado = items

        this.isLoading = false
      } catch (error) {
        console.log(error)
      }
    },

    pagination (pageNum) {
      const { path, query } = this.$route

      this.pageNumber = pageNum

      this.$router.push({ path, query: { ...query, page: pageNum } })

      window.scrollTo(0, 0)
    }
  }
}
</script>

<style scoped>
  .resultados {
    overflow: hidden;
  }

  .resultados__header {
    align-items: center;
    justify-content: center;
    margin-top: 20px;
  }

  .resultados__header-brand {
    margin: 0;
  }

  .resultados__header-brand > a {
    color: var(--color-primary);
    display: table;
    font-weight: 500;
    font-size: 20px;
    margin-bottom: 15px;
    text-decoration: none;
  }

  .resultado__content {
    margin-top: 20px;
    padding-left: 1px;
    padding-right: 1px;
  }

  .resultado__content-item {
    list-style: none;
  }

  @media (min-width: 768px) {
    .resultados__header {
      justify-content: flex-start;
      margin-top: 30px;
    }

    .resultados__header-brand > a {
      margin-bottom: 0;
    }

    .resultado {
      margin-left: -15px;
      margin-right: -15px;
    }
  }
</style>

<style>
  /* pagination */
  .pagination {
    background-color: white;
    border: 1px solid #ccc;
    display: table;
    list-style: none;
    margin: 0 auto 25px auto;
    padding-left: 0;
  }

  .page-item {
    border-left: 1px solid #ccc;
    color: var(--color-primary);
    font-weight: 500;
    float: left;
  }

  .page-item.active {
    background-color: var(--color-primary);
    border-left: 1px solid var(--color-primary);
    color: white;
  }

  .page-item.disabled {
    color: var(--color-font-primary);
  }

  .page-item.active,
  .page-item.disabled {
    cursor: auto;
    pointer-events: none;
  }

  .page-item:hover:not(.active) {
    background-color: #e9ecef;
  }

  .page-link {
    display: block;
    padding: 10px 9px;
  }

  .page-link,
  .controlls > a {
    outline-color: var(--color-primary);
  }

  .controlls {
    float: left;
  }

  .controlls:last-child {
    border-left: 1px solid #ccc;
  }

  .controlls > a {
    color: var(--color-primary);
    display: block;
    font-weight: bolder;
    padding: 10px 15px;
  }

  .controlls.disabled > a {
    color: var(--color-font-primary);
  }

  .controlls.disabled > a {
    cursor: auto;
    pointer-events: none;
  }

  @media (min-width: 768px) {
    .page-link {
      padding: 10px 15px;
    }

    .controlls > a {
      padding: 10px 15px;
    }
  }
</style>
