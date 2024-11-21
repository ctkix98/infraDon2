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
      const pouchDB = new PouchDB('http://admin:admin@localhost:5984/bikeshop')
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

    /*
    non async 
    */
    getIdInitialProblemVersion1(nomObjet: string) {
      console.log('--------- getIdInitialProblemVersion1 ---------')
      this.storage?.allDocs({ include_docs: true }).then((result) => {
        const doc: any = result.rows.find((row: any) =>
          row.doc?.velos?.some((velo: any) => velo.nom === nomObjet)
        );
        if (doc) {
          console.log("Vélo trouvé", "getIdInitialProblemVersion1");
          return doc.doc._id; // Assurez-vous d'utiliser _id, pas doc_id
        } else {
          console.log("Vélo non trouvé");
          return null;
        }
      }).catch((error) => {
        console.error("Erreur lors de l'ajout du document :", error)
        return null
      })
    },

    /*
    retourne l'appel à allDocs 
    */
    getIdInitialProblemVersion2(nomObjet: string) {
      console.log('--------- getIdInitialProblemVersion2 ---------')
      return this.storage?.allDocs({ include_docs: true }).then((result) => {
        const doc: any = result.rows.find((row: any) =>
          row.doc?.velos?.some((velo: any) => velo.nom === nomObjet)
        );
        if (doc) {
          console.log("Vélo trouvé", "getIdInitialProblemVersion2");
          return doc.doc._id; // Assurez-vous d'utiliser _id, pas doc_id
        } else {
          console.log("Vélo non trouvé", "getIdInitialProblemVersion2");
          return null;
        }
      }).catch((error) => {
        console.error("Erreur lors de l'ajout du document :", error)
        return null
      })
    },

    /*
      ne retourne en réalité rien
      comme les return sont dans les then et catch de l'appel allDocs, nous sommes hors de notre composant
      il faudrait dans ce cas utiliser une variable (comme datas) et un accesseur à notre composnant avec un 
      const self = this   
    */
    getIdInitialProblemVersion3(nomObjet: string) {
      console.log('--------- getIdInitialProblemVersion3 ---------')
      this.storage?.allDocs({ include_docs: true }).then((result) => {
        const doc: any = result.rows.find((row: any) =>
          row.doc?.velos?.some((velo: any) => velo.nom === nomObjet)
        );
        if (doc) {
          console.log("Vélo trouvé", "getIdInitialProblemVersion3");
          return doc.doc._id; // Assurez-vous d'utiliser _id, pas doc_id
        } else {
          console.log("Vélo non trouvé");
          return null;
        }
      }).catch((error) => {
        console.error("Erreur lors de l'ajout du document :", error)
        return null
      })
    },


    /*
    async : ici, la method attends que le alldocs.then ou le catch soit exécuté
    une fois que le then ou le catch ont retourné quelque chose, alors la method getId retourne bien le résultat du then ou du catch
    */
    async getId(nomObjet: string) {
      console.log('--------- getId ---------')
      // Retourne directement la promesse
      return await this.storage?.allDocs({ include_docs: true }).then((result) => {
        const doc: any = result.rows.find((row: any) =>
          row.doc?.velos?.some((velo: any) => velo.nom === nomObjet)
        );
        if (doc) {
          console.log("Vélo trouvé");
          return doc.doc._id;
        } else {
          console.log("Vélo non trouvé");
          return null;
        }
      }).catch((error) => {
        console.error("Erreur lors de l'ajout du document :", error)
        return null;
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
    },

    getInput(id :string) {
      const inputElement = document.querySelector(`.input-${id}`) as HTMLInputElement; //faire une classe dynamique, pour recevoir bien l'input qu'il faut changer
      if (inputElement) {
        //console.log(inputElement)
        return inputElement.value;
      }
      return null; // Ou une valeur par défaut, ex. : ""
    },


    //Modifier la DB
    async updateDoc(id: string, newName: string | null = null) { //passer en paramètre l'id de ce que l'on veut modifier, comme pour le remove
    if(newName === null || newName ===""){
      console.log("C'est la merde")
      return;
    }
    try {
      // Charger le document existant
      const doc = await this.storage?.get(id);

      if (doc && doc.velos && doc.velos.length > 0) { //ici on vérifie que le document existe, que le truc velos existe, et que la taille du doc soit plus grand que 0
        // Modifier le nom du premier vélo
        doc.velos[0].nom = newName;

        // Sauvegarder le document mis à jour
        const response = await this.storage?.put(doc);
        console.log("Document mis à jour avec succès :", response);
        this.fetchData()

        // Mettre à jour localement dans `datas` pour refléter les changements
        const index = this.datas.findIndex((item) => item._id === id);
        if (index !== -1) {
          this.datas[index].velos[0].nom = newName;
        }
      } else {
        console.warn("Document ou vélos introuvables");
      }
    } catch (error) {
      console.error("Erreur lors de la mise à jour :", error);
    }

  




    }
  },



  // Hook mounted pour initialiser la base de données lors du montage du composant
  async mounted() {
    this.initDatabase()
    //this.fetchData()
    //this.getInfo()
    this.getName()
    //this.createNewDoc(this.getFakeDoc()) --> déjà ajouté à dans la DB
    //this.getName();
    //this.removeDoc(await this.getId("Velo de Montagne XTRail")) --> a déjà été retiré de la DB
    //this.getName()


    console.log('**** GET ID ****')

    /*
    CASE 1 : WORKING CASE
    */

    let velo = await this.getId("Velo de Montagne XTRail");
    // comme j'ai bien attendu 'await' la réponse des then et catch, j'ai bien trouvé mon vélo, cela fonctionne
    console.log('velo async', velo);
    /* Voici ce que j'obtiens chez moi
    --------- getId ---------
    CedrineView.vue:95 Vélo trouvé
    CedrineView.vue:174 velo async 30e5c000d1e0daf435860249b20087ba
    */

    /*
    CASE 2 : KO
    */
    velo = this.getIdInitialProblemVersion1("Velo de Montagne XTRail");
    console.log('velo non async', velo);
    /* Voici ce que j'obtiens chez moi
    --------- getIdInitialProblemVersion1 ---------
    CedrineView.vue:183 velo non async undefined
    CedrineView.vue:70 Vélo trouvé

    1) Comme je n'ai pas attendu, les instructions sont joués les unes rapidement à la suite des autres, donc,les instructions
      velo = this.getIdInitialProblemVersion1
    et 
      console.log
    s'enchaînent, donc on log undefined, car en réalité, l'apple de la méhode getIdInitialProblemVersion1 n'est encore terminé,
    cependant quand l'appel de la méthode est terminé, on voit bien qu'il trouve un vélo en affichant console.log("Vélo trouvé")
    */


    /*
    CASE 3 : KO
    */
    velo = this.getIdInitialProblemVersion2("Velo de Montagne XTRail");
    // Ici, la method retourne une promise, donc, il faut que je fasse un then et/ou catch
    velo.then(() => { console.log('execution ok') }).catch((error: any) => { console.log('error', error) })
    // Mais je n'ai pas le vélo en soit
    console.log('velo retrun promise async', velo);


    /*
    CASE 4 : KO
    */
    velo = this.getIdInitialProblemVersion3("Velo de Montagne XTRail");
    // Même si cette méthode finit par trouvere le vélo, elle ne renvoie en réalité rien, elle n'attend pas l'appel à la DB
    // donc, cela retourne undefined
    console.log('velo no return', velo);


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
  <h1>Liste des vélos disponibles</h1>
  <ul>
    <li v-for="doc in datas" :key="doc._id">
      <h3>ID: {{ doc._id }}</h3>
      <ul v-if="doc.velos">
        <li v-for="velo in doc.velos" :key="velo.nom">
          Nom: {{ velo.nom }}
        </li>
      </ul>
      <button @click="removeDoc(doc._id)">Supprimer</button>
      <input :class="'input-' + doc._id" type="text" :placeholder="'Nouveau nom pour ' + doc.velos[0]?.nom" />
      <button class="update" @click="() => updateDoc(doc._id, getInput(doc._id))">
        Modifier le document
      </button>
    </li>
  </ul>
</template>

<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}

.update {
  margin-left: 15px;
}

.input {
  margin-left: 15px;
}
</style>
