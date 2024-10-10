<script lang="ts">
import PouchDB from 'pouchdb'
export default {
  data() {
    return {
      datas: [], // Stockage des documents
      databaseReference: null, // Référence à la base de données
      storage: null as PouchDB.Database | null
    };
  },

  methods: {
    // Initialisation de la base de données
    initDatabase() {
      const $pouchDB = new PouchDB('http://localhost:5984/bikeshop');
      if ($pouchDB) {
        console.log('Connection with PouchDB established successfully')
        console.log($pouchDB)
      } else {
        console.warn('Something went wrong')
      }
 
      this.storage = $pouchDB
    },
 
    fetchData() {
      this.storage?.allDocs().then(function (doc) {
        console.log(doc)
      })
    }
  },

  // Hook mounted pour initialiser la base de données lors du montage du composant
  mounted() {
    this.initDatabase();
    this.fetchData();
  },
};
</script>

<template>
  <div class="cedrine">
    <h1>This is a Cedrine page</h1>
  </div>
  <!--<div>
    <button @click="Inc">
      Count is: {{ total }}
    </button>
  </div> -->
</template>

<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}
</style>
