<template>
  <body>
    <div>
      <Map/>
    </div> 
  </body>
</template>


<script>
import 'bootstrap/dist/css/bootstrap.min.css'

import Map from "./components/Map.vue"

export default {
  name: "App",
  components: {
    Map,
  },
};
</script>

<style lang="scss" scoped>
</style>
