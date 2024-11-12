<script lang="ts">
import PouchDB from 'pouchdb'
export default {
  data() {
    return {
      datas: [] as any[], // Stockage des documents, et mettre ici que c'est un tableau vide, de n'importe quel type
      storage: null as PouchDB.Database | null // Référence à la base de données
    }
  },

  methods: {
    // Initialisation de la base de données
    initDatabase() {
      const pouchDB = new PouchDB('http://localhost:5984/bikeshop')
      if (pouchDB) {
        console.log('Connection with PouchDB established successfully')
        //console.log($pouchDB)
      } else {
        console.warn('Something went wrong')
      }
      this.storage = pouchDB
    },

    fetchData() {
      this.storage?.allDocs().then(function (doc) {
        console.log('Ce sont toutes les données')
        console.log(doc)
      })
    },

    //Récupère les infos générales de la DB
    getInfo() {
      this.storage?.info().then(function (info) {
        console.log('ce sont les infos')
        console.log(info)
      })
    },

    //Récupérer
    getName() {
      const self = this //donc pour récupérer l'objet, on fait une constante
      // ici dans mon component
      this.storage?.allDocs({ include_docs: true }).then((result) => {
        // ici je suis dans l object base de donnees
        // this = base de donnees
        self.datas = result.rows.map((row) => row.doc)
        console.log('Les noms des vélos')
        this.datas.forEach((doc: any) => {
          doc.velos.forEach((velo: any) => {
            console.log(`${velo.nom}`)
          })
        })
      })


      //console.log(`Ce sont les données ${donnees}`);
      //this.storage?.get('5950d965e89ee73edf2ed20ed2003cc3').then(function(doc){
      //console.log(doc)
      //})
      //console.log("j'imprime un nom ici...")
      //console.log(this.storage?.name)
    },

    async getId(nomObjet: string) {
      // Retourne directement la promesse
      return await this.storage?.allDocs({ include_docs: true }).then((result) => {
        const doc: any = result.rows.find((row: any) =>
          row.doc?.velos?.some((velo: any) => velo.nom === nomObjet)
        );
        if (doc) {
          return doc.doc._id; // Assurez-vous d'utiliser _id, pas doc_id
        } else {
          console.log("Vélo non trouvé");
          return null;
        }
      }).catch((error) => {
        console.error("Erreur lors de l'ajout du document :", error)
      })
    },

    //Créer un nouveau document
    createNewDoc(newDoc: any) {
      this.storage?.post(newDoc)
        .then((response) => {
          console.log('Document ajouté avec succès :', response)
          if (response?.ok) {
            this.datas.push({ ...newDoc, _id: response.id, _rev: response.rev }) // Mise à jour locale avec ID et révision
          }
        })
        .catch((error) => {
          console.error("Erreur lors de l'ajout du document :", error)
        })
    },

    removeDoc(id: string) {
      if (id === null) {
        console.log("Aucun document ne peut être retiré")
        return;
      }

      this.storage?.get(id)
        .then(doc => {
          return this.storage?.remove(doc);
        }).then(result => {
          console.log("Document supprimé avec succès :", result);
          // Supprime également le document de `datas` local si nécessaire
          this.datas = this.datas.filter(d => d._id !== id);
        })
        .catch(err => {
          console.log("Erreur lors de la suppression :", err);
        });
    },

    getFakeDoc() {
      const nouveauVelo = {
        velos: [
          {
            nom: 'Velo de Course Speedster',
            description: 'Parfait pour les amateurs de vitesse en milieu urbain.',
            prix: 1300,
            couleur: 'Jaune',
            avis: [
              {
                utilisateur: 'Marc',
                commentaire: 'Léger et rapide, idéal pour les trajets en ville.'
              }
            ]
          }
        ]
      }
      return nouveauVelo;
    }
  },

  // Hook mounted pour initialiser la base de données lors du montage du composant
  async mounted() {
    this.initDatabase()
    //this.fetchData()
    this.getInfo()
    this.getName()
    //this.createNewDoc(this.getFakeDoc())
    //this.getName();
    const velo = await this.getId("Velo de Montagne XTRail");
    console.log(this.getId("Velo de Montagne XTRail"))

    //this.removeDoc(this.getId())
    //this.getName()
  }
}
</script>

<template>
  <div class="cedrine">
    <h1>This is a Cedrine page</h1>
  </div>
  <div>
    <button @click="createNewDoc(getFakeDoc())">
      Ajouter un document
    </button>
  </div>
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
