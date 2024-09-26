<template>
  <div class="cedrine">
    <h1>This is a Cedrine page</h1>
  </div>
  <div>
    <button @click="count++">
    Count is: {{ count }}
  </button>
  </div>
</template>

<script>
import { ref } from 'vue'

export default{
  setup() {
    const count = ref(0)  // Déclare la valeur réactive

    return {
      count
    }
  }
}
</script>



<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}
</style>
