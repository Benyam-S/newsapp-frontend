<template>

    <div class="input-group me-2 search-bar">
        <input type="search" class="form-control pt-2 pb-2 ps-3" v-model="text" placeholder="Search" aria-label="Search"
            aria-describedby="search-addon" @search="onSearch" />
        <button type="button" v-on:click="onSubmit" class="btn btn-primary">search</button>
    </div>

</template>

<script>
export default {
    name: 'SearchBar',
    data() {
        return {
            text: ''
        }
    },
    methods: {
        onSubmit() {
            this.text = this.text.trim(); // trimming the white spaces

            // Only emit event if the text is longer than 2 characters
            if (this.text.length > 2) {
                this.$emit('search-article', this.text)
            }
        },

        onSearch() {
            // This function will be called when 'x' button in search bar is clicked
            this.$emit('on-search')
        }
    },
}
</script>

<style scoped>
.search-bar {
    flex: 1;
}
</style>