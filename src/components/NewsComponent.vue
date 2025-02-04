<template>
    <div class="col-xs-6">
        <div v-if="isLoading">
            Loading TechCrunch articles...
        </div>
        <div v-if="!isLoading">
            <h1>
                News
            </h1>
            <div class="card w-100" style="width: 18rem;" v-for="article in articles" :key="article.publishedAt">
            <div class="card-body">
                <h5 class="card-title">{{ article.title }}</h5>
                <h6 class="card-subtitle mb-2 text-muted">{{ article.author }} | {{ article.publishDate }}</h6>
                <p class="card-text">{{ article.description }}</p>
                <a target="_blank" class="card-link" :href="article.url">View article</a>
            </div>
        </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
import moment from 'moment';

export default {
  name: 'NewsComponent',
  data() {
    return {
      isLoading: true,
      articles: []
    };
  },
  methods: {
    async getData() {
         axios.get('http://api.mediastack.com/v1/news', {
            params: {
               sources: 'techcrunch',
               access_key: '71dcb1992a01fe6594052b9bf5810ab5',
               countries: 'us',
               limit: 5
            }
        })
        .then(response => {
            this.articles = response.data.data.map(article => {
              return { ...article, publishDate: moment(article.published_at).format('MM/DD/YYYY h:mm A') };
            });

            this.isLoading = false;
        });
    }
  },
  beforeMount() {
    this.getData();
  }
}
</script>