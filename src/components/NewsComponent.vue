<template>
    <div class="col-xs-6">
        <div v-if="isLoading">
            Loading TechCrunch articles...
        </div>
        <div v-if="isError">
            Error loading news component.
        </div>
        <div v-if="!isLoading && !isError">
            <h1>
                News
            </h1>
            <div class="card w-100" style="width: 18rem;" v-for="article in articles" :key="article.publishedAt">
            <div class="card-body">
                <h5 class="card-title">{{ article.title }}</h5>
                <h6 class="card-subtitle mb-2 text-muted">{{ article.author }} | {{ article.publishDate }}</h6>
                <p class="card-text">{{ article.parsedArticleDescription }}</p>
                <a target="_blank" class="card-link" :href="article.url">View article</a>
            </div>
        </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
import moment from 'moment';
import { parse } from 'node-html-parser';

export default {
  name: 'NewsComponent',
  data() {
    return {
      isLoading: true,
      isError: false,
      articles: []
    };
  },
  methods: {
    async getData() {
        axios.get('https://news-weather-api.vercel.app/api/news').then(response => {
            const data = response.data.map(article => {
              return {
                 ...article,
                 parsedArticleDescription: parse(article.description).textContent,
                 publishDate: moment.utc(article.publishedAt).local().format('MM/DD/YYYY h:mm A')
              };
            });
            this.articles = data;
            this.isLoading = false;
        }).catch(e => {
            this.isLoading = false;
            this.isError = true;
            console.log(e);
        });
    }
  },
  beforeMount() {
    this.getData();
  }
}
</script>