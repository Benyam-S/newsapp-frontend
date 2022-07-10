<template>
    <header class="header">
        <h1 class="title">
            <nuxt-link to="/">News Articles</nuxt-link>
        </h1>
    </header>

</template>

<script>
export default {
    name: "AppHeader"
}
</script>

<style>
.header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 1rem;
    padding-bottom: 1rem;
    border-bottom: 1px dotted #ccc;
}

.header .title {
    font-size: 3rem;
    display: inline-block;
    background: #333;
    padding: 0.3rem 1rem;
    margin-right: 0.5rem;
}

.header a {
    color: #fff !important;
}
</style>

