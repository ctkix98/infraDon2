<template>
  <div class="cedrine">
    <h1>This is a Cedrine page</h1>
  </div>
  <div>
    <button @click="Inc">
      Count is: {{ total }}
    </button>
  </div>
</template>

<script lang="ts">
export default {
  data() {
    //return {
      //total: 10,  
    //};
  },
  methods: {
    //Inc() {
      //this.total++; 
    //},
  },
};
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
