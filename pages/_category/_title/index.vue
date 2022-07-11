<template>
    <div>
        <p class="p-0 m-0 text-end text-small">{{ new Date(article.publishedAt).toDateString() }}</p>

        <h2 class="mb-5">{{ article.title }}
            <span><a :href="article.url" target="_blank">
                    <fa icon="up-right-from-square"></fa>
                </a>
            </span>
        </h2>

        <div class="container m-0 p-0">
            <img class="w-100" :src="article.urlToImage" />
        </div>
        <p class="mt-5">description: {{ article.description }}</p>
        <p>{{ article.content }}</p>
        <p class="text-end text-small">Author: {{ article.author }}</p>
    </div>
</template>


<script>
import AppHeader from '../../../components/AppHeader.vue'
import axios from 'axios';
import config from '../../../config/config';

export default {
    data() {
        return {
            article: {},
        }
    },
    components: { AppHeader },
    created() {

        let title = this.$route.params.title;
        let category = this.$route.params.category;
        let url = `${config.baseURL}/articles/${category}/${title}`;
        const response = axios.get(url);
        response.then((result) => {
            if (result.data && result.data.status) {
                this.article = result.data.article;
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
}
</script>

<style scoped>
.url {
    font-size: small;
    color: #0d6efd;
}
</style>
