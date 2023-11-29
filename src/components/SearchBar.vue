<template>
    <nav class="navbar mw-100">
        <form class="d-flex w-75 mx-auto" id="queryForm">
            <input class="form-control mx-3" type="search" placeholder="Search Locations" ref="search">
            <button class="btn btn-outline-success my-2 my-sm-0" @click.prevent="emitEventChanged()">Search</button>
        </form>
    </nav>
</template>


<script>
export default {
    emits: ["query"],
    methods: {
        emitEventChanged() {
            this.$emit('query', this.$refs.search.value)
            document.getElementById("queryForm").reset();
        },
    },
}
</script>
