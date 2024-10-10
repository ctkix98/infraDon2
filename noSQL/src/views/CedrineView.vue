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
        //console.log($pouchDB)
      } else {
        console.warn('Something went wrong')
      }
 
      this.storage = $pouchDB
    },
 
    fetchData() {
      this.storage?.allDocs().then(function (doc) {
        console.log("Ce sont toutes les données")
        console.log(doc)
      })
    },

    //Récupère les infos générales de la DB
    getInfo(){
    this.storage?.info().then(function(info){
      console.log("ce sont les infos")
      console.log(info)
    })
    },

    //Récupérer
    getName(){
      this.storage?.allDocs({include_docs :true}).then(result => {
        this.datas = result.rows.map(row =>row.doc);
        console.log("Les noms des vélos")
        this.datas.forEach((doc:any)=>{
          doc.velos.forEach((velo: any)=>{
            console.log(`${velo.nom}`);
          })
          
        })
      })

      
      
      //console.log(`Ce sont les données ${donnees}`);
      //this.storage?.get('5950d965e89ee73edf2ed20ed2003cc3').then(function(doc){
        //console.log(doc)
      //})
      //console.log("j'imprime un nom ici...")
      //console.log(this.storage?.name)


    }


  },


  // Hook mounted pour initialiser la base de données lors du montage du composant
  mounted() {
    this.initDatabase();
    this.fetchData();
    this.getInfo();
    this.getName();
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
