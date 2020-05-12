<template>
  <div class="home">
    <pricing/>
  </div>
</template>

<script>
// @ is an alias to /src

import Calendar from "../components/Calendar";
import Pricing from "../components/Pricing";
export default {
  name: 'home',
  components: {
    Pricing,
  }
}
</script>
