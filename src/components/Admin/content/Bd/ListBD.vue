<template>
    <div class="gestionInspecteur"  >

      <h3>Liste des BORDEREAUX D'ENVOI</h3>
      <div class="rechercher-table">

      
            <div class="rechercher">
                <input type="text" v-model="rechercher" placeholder="Recherche un affaire avec Bon Commande">
            </div>
                <p v-if="flagMsgBonCommande & succes == true" :class="{ succes: succes }">
                {{ msgBonCommande }}
            </p>

              <p v-if="flagMsgBonCommande && echec == true" :class="{ echec: echec }">
                  {{ msgBonCommande }}
              </p>

            <table id="inspecteurs">
              <tr>
                <th>  </th>
                <th>BORDEREAU D’ENVOI N°</th>
                <th>N°. Affaire</th>
                <th>Réf. Client</th>
                <th>RaisonSociale</th>
                <th>N° du BC de Client</th>
                <th>Date d'envoi</th>
                <th>Date Reçu</th>
                <th>Téléphone</th>
                <th>Date</th>
                <th>Actions</th>

              </tr>
              <tr v-for="(bordereau, i) in filterBordereaus" :key="bordereau._id">
                <td>
                  <input type="checkbox" :value="bordereau._id" v-model="checkedClients" style="width: 20px;">
                </td>
                <td>{{ bordereau.numeroBD }}</td>
                <td>{{ bordereau.numeroAffaire }}</td>
                <td>{{ bordereau.refClient }}</td>
                <td>{{ bordereau.raisonSocial }}</td>
                <td>{{ bordereau.numeroBC }}</td>
                <td>{{ new Date(bordereau.dateEnvoi).toLocaleDateString() }}</td>
                <td>{{ new Date(bordereau.dateRecu).toLocaleDateString() }}</td>
                <td>{{ bordereau.telephone }}</td>
                <td>{{ bordereau.dateCreation }}</td>
                <td @click="deleteBDWithoutFilenameBE(bordereau.affaireId, bordereau.numeroAffaire, i)"><i class="fa-solid fa-trash"></i></td>
              </tr>
            </table>

            <div class="deleteAll" v-show="checkedClients.length > 1">
              <input type="submit" value="Supprimer tout" @click="deleteClients()">
            </div>

            <!-- <ul>
                <li v-for="number in numberPage" :key="number" @click="pagination(number)">
                  {{ number }}
                </li>
            </ul> -->


      </div> 




  </div>

</template>

<script>
import Service from "../../../../Service";
// import InfoClient from "./InfoClient.vue";
export default {
  name: "gestionInterlocuteur",
  components: {
  },
  data() {
    return {
      affaireId : null,
      rechercher: null,
      affaires : [],
      missions : [],
      clients : [],
      bordereaus: [],
      checkedClients : [],
      msg : null,
      succes : null,
      echec: null,
      flagMsgBonCommande : null,
      msgBonCommande: null

    };
  },
  methods: {

    deleteBDWithoutFilenameBE(affaireId, numeroAffaire, index) {
      Service.DeleteBDWithoutFilenameBE(affaireId, numeroAffaire)
      .then((result) => {
        if(result) {
          this.bordereaus.splice(index, 1);
        }
      })
      .catch((error) => {
          console.log(error.message);
      });
    }
  },

  computed : {
      filterBordereaus() {
            return this.bordereaus.filter((item) => {
              if(!this.rechercher)
              {
                return item         
              } 
                const date = new Date(item.date).toLocaleDateString()
                const dateRecu = new Date(item.dateRecu).toLocaleDateString()
                const dateEnvoi = new Date(item.dateEnvoi).toLocaleDateString()
                return !item.numeroBD.indexOf(this.rechercher) 
                       || !date.indexOf(this.rechercher) 
                       || !dateRecu.indexOf(this.rechercher) 
                       || !dateEnvoi.indexOf(this.rechercher) 
                       || !item.numeroAffaire.toString().indexOf(this.rechercher.toString()) 
                       || !item.refClient.toString().toLowerCase().indexOf(this.rechercher.toString().toLowerCase()) 
                       || !item.raisonSocial.toLowerCase().indexOf(this.rechercher.toLowerCase()) 
                       || !item.numeroBC.toString().indexOf(this.rechercher.toString()) 
                       || !item.numeroICE.toString().indexOf(this.rechercher.toString())
                       || !item.telephone.toString().indexOf(this.rechercher.toString())
            })
      }
  },

  created() {

// read all be

Service.readAllBordereaus()
.then((response) => {
  this.bordereaus = response.data.bordereaus;
})
.catch((err) => {
  console.log(err);
})




 // read all Affaires LOCAL
      Service.readAllAffaires()
      .then((result) => {      
         result.data.result.forEach((element) => {
           if(element.renseignerIntervention != 'false')
           {
            this.affaires.push(element);
           }
         })

      })
      .catch((error) => {
          this.msg = error.message;
          console.error(`HTTP error: ${error.name} => ${error.message}`);
          throw "fail request at: GET /refreshtime";
      });

      // Read all missions
      Service.readAllMissions()
      .then((result) => {
        this.missions = result.data.missions;
      })
      .catch((error) => {

              this.msg = error.message;
              console.error(`HTTP error: ${error.name} => ${error.message}`);
              throw "fail request at: GET /refreshtime";
      });


      // Read all clients and check rapport exist or no 
      Service.readClient()
      .then((result) => {
        this.clients = result.data.clients;
             // check rapports exist and valide
              Service.checkRapports()
              .then((result) => {
                var objectsFound = [];
                // search db atlas cloud
                for(let objectNumber in result.clients){
                    var nomSociete = result.clients[objectNumber].nomSociete;   
                    var idSociete = result.clients[objectNumber]._id;
                    //search db local
                    this.clients.forEach(element => {
                      if(element.raisonSocial == nomSociete)
                      {
                         // serach rappport with consition (confirmation & clientId)
                         result.rapports.forEach(element => {
                          if(element.confirmation == 1 && element.clientId == idSociete){
                            objectsFound.push(nomSociete);
                          }
                         })

                      }
                    });
                }

              })
              .catch((error) => {
                        console.log(error)
              });

      })
      .catch((error) => {

              this.msg = error.message;
              console.error(`HTTP error: ${error.name} => ${error.message}`);
              throw "fail request at: GET /refreshtime";
      });


  }

};
</script>

<style scoped>
.succes {
  background-color: green;
  color: white;
  padding: 10px;
  height: fit-content;
  width: 100%;
}

.echec {
  background-color: red;
  color: white;
  padding: 10px;
  height: fit-content;
  width: 100%; 
}

.gestionInspecteur {
  width: 100%;
  height: 100%;
  padding-top: 10px;
  padding-left: 10px;
  margin: 0px;
  position: relative;
}
.gestionInspecteur h3 {
  width: 100%;
  height: fit-content;
  padding: 5px;
  margin: 0;
  color: white;
  background-color: #243064;
  text-align: center;
  margin-bottom: 10px;
}

.gestionInspecteur .rechercher-table {
  width: 100%;
  height: 100%;
}
.gestionInspecteur .rechercher-table .rechercher {
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  margin-bottom: 10px;

}
.gestionInspecteur .rechercher-table .uplode {
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
  margin-bottom: 10px;

}
.gestionInspecteur .rechercher-table .uplode input {
  margin-bottom: 10px;
  height: 40px;
  width: 30%;
}

.gestionInspecteur .rechercher-table .rechercher input {
  width: 50%;
  height: 40px;
  outline: 0;
  border: 1px solid #243064;
  font-size: 16px;
  background-color: white;
  color :#243064;
}

.gestionInspecteur .rechercher-table table  {
  width: 100%;
}
.gestionInspecteur .rechercher-table table tr  {
  width: 100%;
}
.gestionInspecteur .rechercher-table table tr th {
    background-color: #243064;
    color: white;
    padding: 2px;
}

.gestionInspecteur .rechercher-table table tr td {
    background-color: #ddd;
    color: black;
    padding: 2px;
}
.gestionInspecteur .rechercher-table table tr td {
  cursor: pointer;
}

.gestionInspecteur .rechercher-table table tr td a {
  cursor: pointer;
  width: fit-content;
}
.fa-trash-can {
  color: red;
}

.fa-pen-to-square {
  color: blue;
}

.fa-circle-check {
  color: green;
}
.fa-download {
  color: black;
}


.gestionInspecteur .rechercher-table ul {
  width: 100%;
  height: fit-content;
  text-align: center;
  position: absolute;
  bottom: 0;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
}

.gestionInspecteur .rechercher-table ul li {
  color:black;
 margin-left: 2px; 
 cursor: pointer;
 font-size: 8px;
}
.gestionInspecteur .rechercher-table ul li:hover {
  color:red;
 margin-left: 5px;
 transition: 0.3s; 
}


.gestionInspecteur .rechercher-table .deleteAll {
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  margin-top: 10px;
}

.gestionInspecteur .rechercher-table .deleteAll input {
  width: 50%;
  height: 40px;
  background-color: red;
  color: white;
  border: 0;
}

#app > div > div > div.menu-content > div.content > div {
  background-color: white;
}

#inspecteurs > tr > td {
  background-color: white;
  border-bottom: 1px solid #243064;
}

#app > div > div > div.menu-content > div.content > div > h3 {

    background-color: #ff0000d4;
    padding: 15px;
    
}

#inspecteurs > tr > td:nth-child(11) > svg  {
  color: red;
}
</style>