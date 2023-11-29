<template>
    <button type="button" class="btn btn-primary m-3" @click.prevent="getLocation">Get My Location</button>
</template>

<script>
export default {
    methods: {
        getLocation() {
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(
                    (position) => {
                        const pos = {
                            lat: position.coords.latitude,
                            lng: position.coords.longitude,
                        }
                        this.$emit('displayMyLocation', pos)
                    },
                );
            } else {
                console.log(`Fail to get current location`)
            }
        }
    },
}
</script>
