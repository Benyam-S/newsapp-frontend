<template>
    <div class="article">
        <p class="p-0 m-0 text-end text-small"> {{ new Date(article.publishedAt).toDateString() }}</p>
        <nuxt-link :to="article.category + '/' + article.title">
            <p class="h4 text-dark title" v-if="searchKeyExists">{{ setTitle[0] }}<span class="highlighted">{{
                    setTitle[1]
            }}</span>{{ setTitle[2] }}</p>
            <p class="h4 text-dark title" v-else>
                {{ article.title }}
            </p>
        </nuxt-link>
        <p> {{ article.description }}</p>
        <div class="d-flex justify-content-end align-items-center">
            <a :href="article.url" target="_blank">
                <fa icon="up-right-from-square"></fa>
            </a>
            <p class="category" v-on:click="onCategory"> {{ article.category }}</p>
        </div>
    </div>
</template>

<script>
export default {
    name: "Article",
    props: ['article', 'id', 'searchKey'],

    computed: {
        setTitle() {
            let title1 = "";
            let title2 = "";
            let title3 = "";

            let title = this.article.title;
            let key = this.searchKey.toLowerCase();
            let startIndex = title.toLowerCase().indexOf(key);
            let length = key.length;

            if (title.toLowerCase().includes(key)) {
                title1 = title.slice(0, startIndex)
                title2 = title.slice(startIndex, startIndex + length)
                title3 = title.slice(startIndex + length)
            }

            return [title1, title2, title3];
        },

        searchKeyExists() {
            return (this.searchKey && this.article.title.toLowerCase().includes(this.searchKey.toLowerCase()));
        }
    },

    methods: {
        onCategory() {
            this.$emit('on-category', this.article.category)
        },
    }
}
</script>

<style scoped>
.article {
    padding: 1rem;
    border: 1px dotted #ccc;
    margin: 1rem 0;
    overflow: hidden;
}

.article p,
.article div {
    overflow: hidden;
}

.title:hover {
    color: #3e7ee5 !important;
    text-decoration: underline;
}

.title-piece {
    float: left;
}

.category {
    border: solid 1px;
    padding: 2px 10px;
    margin: 0 0 0 10px;
    border-radius: 25px;
    cursor: pointer;
    text-transform: capitalize;
}

.category:hover {
    background: #0d6efd;
    color: white;
}

a,
a:hover {
    text-decoration: none;
    color: #666;
}

.highlighted {
    background: yellow;
}
</style>