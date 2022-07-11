<template>
  <div>
    <h2>Welcome to the best news platform</h2>

    <div>
      <div class="d-flex mt-4 mb-5">
        <SearchBar v-on:search-article="searchArticles" v-on:on-search="onSearchChange" />

        <div class="me-2">
          <select class="form-select pt-2 pb-2 categories" @change="onCategoryChange()" v-model="category"
            aria-label="category">
            <option disabled>Category</option>
            <option value="all">All</option>
            <option :key="categoryValue" v-for="categoryValue in categories" :value="categoryValue">{{ categoryValue }}
            </option>
          </select>
        </div>

        <div>
          <select class="form-select pt-2 pb-2" @change="onSortChange()" v-model="sortBy" aria-label="sort">
            <option disabled>Sort By</option>
            <option value="newest">Newest</option>
            <option value="oldest">Oldest</option>
            <option value="asc">A-Z</option>
            <option value="desc">Z-A</option>
          </select>
        </div>
      </div>

      <div>
        <Article v-on:on-category="onCategoryChange" :key="article.id" v-for="article in articles" :article="article"
          :searchKey="searchKey">
        </Article>
      </div>

      <div class="mt-5">
        <Paginate :value="currentPage" :page-count="pageCount" :margin-pages="2" :page-range="5"
          container-class="pagination" page-class="page-item" page-link-class="page-link-item" prev-class="prev-item"
          prev-link-class="prev-link-item" next-class="next-item" next-link-class="next-link-item"
          break-view-class="break-view" break-view-link-class="break-view-link" active-class="active-class"
          disabled-class="disabled-class" :clickHandler="onPagePropertyChange">
        </Paginate>
      </div>
    </div>
  </div>

</template>

<script>
import axios from 'axios';
import Article from '../components/Article.vue';
import SearchBar from '../components/SearchBar.vue';
import config from '../config/config';
import Paginate from 'vuejs-paginate';

export default {
  name: "IndexPage",
  head() {
    let path = `${config.baseURL}${this.$route.fullPath}`

    return {
      title: "Welcome to News App",
      meta: [
        { hid: 'description', name: 'description', content: "Best news outlet here for free." },
        { hid: 'og:title', property: 'og:title', content: "News Articles" },
        { hid: 'og:url', property: 'og:url', content: path },
        { hid: 'og:description', property: 'og:description', content: "Best news outlet here for free." },
      ],
      link: [
        { hid: "canonical", rel: "canonical", href: path },
      ]
    };
  },

  components: {
    Article,
    SearchBar,
    Paginate,
  },

  data() {
    return {
      articles: [],
      category: "all",
      categories: [],
      sortBy: "newest",
      searchKey: "",
      currentPage: 1,
      pageCount: 0,
    };
  },
  created() {

    // Getting all the categories
    var url = `${config.apiBaseURL}/categories`;
    axios.get(url).then((result) => {
      if (result.data && result.data.status) {
        this.categories = result.data.categories;
      }
    }).catch((result) => {
      console.log(result);
    });

    // Fetching all the articles based on their category
    this.fetchByCategory()
  },

  methods: {

    // onSearchChange function is called when the search bar is cleared
    onSearchChange() {
      this.searchKey = ""
      this.fetchByCategory()
    },

    // searchArticles fetches all the articles that matches the given search key for given category and sorting
    searchArticles(text) {
      this.searchKey = text ?? this.searchKey;

      var url = `${config.apiBaseURL}/articles/${this.category}/search` +
        `?q=${this.searchKey}&page=${this.currentPage}&sortBy=${this.sortBy}`;
      const response = axios.get(url);
      response.then((result) => {
        if (result.data && result.data.status) {
          const { articles, pageCount, pageNum } = result.data;
          this.articles = articles;
          this.currentPage = pageNum;
          this.pageCount = pageCount;
        }
      }).catch((err) => {
        // Redirecting to 404 page if bad request
        if (err.response && err.response.status == 400) {
          this.$nuxt.error({ statusCode: 404, message: (err.response.data.error) })
        } else {
          this.$nuxt.error({ statusCode: 404, message: err.message })
        }
      });
    },

    // onCategoryChange is called when category is changed
    onCategoryChange(category) {
      this.category = category ?? this.category;

      // Check if the previous task was searching if then continue based on the category
      if (this.searchKey) {
        this.searchArticles()
        return;
      }

      this.fetchByCategory()
    },

    // fetchByCategory fetches all the articles based on the given category and sorting
    fetchByCategory() {
      var url = `${config.apiBaseURL}/articles/${this.category}` +
        `?page=${this.currentPage}&sortBy=${this.sortBy}`;
      const response = axios.get(url);
      response.then((result) => {
        if (result.data && result.data.status) {
          const { articles, pageCount, pageNum } = result.data;
          this.articles = articles;
          this.currentPage = pageNum;
          this.pageCount = pageCount;
        }
      }).catch((err) => {
        // Redirecting to 404 page if bad request
        if (err.response && err.response.status == 400) {
          this.$nuxt.error({ statusCode: 404, message: err.response.data.error })
        } else {
          this.$nuxt.error({ statusCode: 404, message: err.message })
        }
      });
    },

    onSortChange() {
      // Check if the previous task was searching if then continue based on the category
      if (this.searchKey) {
        this.searchArticles()
        return;
      }

      this.fetchByCategory()
    },

    // onPagePropertyChange is called when the pagination changes
    onPagePropertyChange(pageNum) {
      this.currentPage = pageNum;

      // Check if the previous task was searching if then continue based on the category
      if (this.searchKey) {
        this.searchArticles()
        return;
      }

      this.fetchByCategory()
    },
  }
}
</script>

<style>
select {
  text-transform: capitalize;
}

.pagination {
  justify-content: center;
  align-items: center;
}

.page-item {
  border-radius: 0;
  border: 1px solid #ecedee;
  height: 40px;
  width: 40px;
  text-align: center;
  justify-content: center;
  align-items: center;
  display: flex;
  margin: 0px 5px;
  cursor: pointer;
}

.page-item:hover {
  background-color: #0d6efd;
}

.page-item:hover .page-link-item {
  color: white;
}

.page-item .page-link-item {
  color: black;
  text-decoration: none !important;
}

.prev-item {
  margin-right: 15px;
}

.prev-item:hover .prev-link-item {
  color: #0d6efd;
}

.prev-item .prev-link-item {
  color: black;
  text-decoration: none !important;
}

.next-item {
  margin-left: 15px;
}

.next-item:hover .next-link-item {
  color: #0d6efd;
}

.next-item .next-link-item {
  color: black;
  text-decoration: none !important;
}

.active-class {
  background-color: #0d6efd !important;
  color: white;
}

.active-class a {
  color: white !important;
}

.disabled-class {
  pointer-events: none;
  cursor: default;
}

.disabled-class a {
  color: gray !important;
}
</style>