<template>
    <div class="gestionInspecteur"  >

      <h3 v-if="flagImportFacture == false">Facture Caisse</h3>
      <div v-if="flagImportFacture == false" class="rechercher-table">


            <div class="rechercher">
                <input type="text" v-model="rechercher" placeholder="Recherche un affaire avec Facture Client">
            </div>

              <Traitement :msg="msgPaiement" v-if="traitement == true"/>

              <div class="uplode" v-if="flagInsertEmise == true">

                  <h4>Information Paiement Facture </h4>

                  <div >
                      <label for="Paiement">Type de Paiement</label>
                      <select v-model="typePaiement" @change="selectPaiement">
                        <option v-for="(payment, index) in payments" :key="index" :value="payment.type" > {{ payment.type }}</option>
                      </select>
                  </div>

                  <div >
                      <label for="refTransa">Référence de transaction ou bénéficiaire</label>
                      <input type="text" v-model="refTransaction">
                  </div>

                  <div >
                      <label for="refTransa">Document de preuve</label>
                      <input type="file" multiple="multiple" class="file" placeholder="Fichier PDF" ref="file" @change="previewFile">
                  </div>
                  <div >
                      <input type="hidden" v-model="targetAffaire">
                  </div>

                  <div>
                      <input type="submit" value="Importer Preuve Paiement" @click="enregitreCheque()">
                  </div>

              </div>



            <table id="inspecteurs">
              <tr>
                <th>  </th>
                <th>Date</th>
                <th>Numéro d'affaire</th>
                <th>Réf Client</th>
                <th>Raison sociale </th>
                <th>mission</th>
                <th colspan="7">Status</th>

              </tr>
              <tr v-for="(affaire) in filterAffaires" :key="affaire._id">
                <td>
                  <input type="checkbox" :value="affaire._id" v-model="checkedClients" style="width: 20px;">
                </td>
                <td>{{  new Date(affaire.date).toLocaleDateString() }}</td>
                <td>{{ affaire.numeroAffaire }}</td>
                <td>
                    <p v-for="(client, index) in clients" :key="index">{{ affaire.numeroAffaire.substr(13) == client.raisonSocial  ? client.refClient : '' }}</p>
                </td>
                <td>
                     <p v-for="(client, index) in clients" :key="index">{{ affaire.numeroAffaire.substr(13) == client.raisonSocial  ? client.raisonSocial : '' }}</p>
                </td>
                <td v-if="affaire.numeroAffaire">
                    <p v-for="(mission, index) in missions" :key="index"> {{ mission.numeroAffaire == affaire.numeroAffaire  ? mission.codeMission : '' }}</p>
                </td>



                <td v-if="affaire.nameFileAffaire != 'false'" style="background-color: white; margin : 0; padding: 0; padding-bottom: 5px; padding-left: 5px;" @click="displayDEVIS(affaire.nameFileAffaire)"><p style="font-size: 25px; color:green; font-wight: bold;font-weight: bold;">P</p><i class="fa-solid fa-download" style="font-size: 25px; color:green;"></i></td>
                <td v-if="affaire.nameFileAffaire == 'false'" style="background-color: white; margin : 0; padding: 0; padding-bottom: 5px; padding-left: 5px;"><p style="color: red; font-size: 25px;">P</p><i class="fa-solid fa-forward-step" style="font-size: 25px; color:red;"></i></td>

                <td v-if="affaire.bonCommande != 'false'" style="background-color: white; margin : 0; padding: 0; padding-bottom: 5px; padding-left: 5px;" @click="displayBC(affaire.bonCommande)"><p style="font-size: 25px; color:green; font-wight: bold; font-weight: bold;">AP</p><i class="fa-solid fa-download" style="font-size: 25px; color:green;"></i></td>
                <td v-if="affaire.bonCommande == 'false'" style="background-color: white; margin : 0; padding: 0; padding-bottom: 5px; padding-left: 5px;"><p style="font-size: 25px; color:red; font-wight: bold; font-weight: bold;">AP</p><i class="fa-solid fa-forward-step" style="font-size: 25px; color:red;"></i></td>

                <td v-if="affaire.renseignerIntervention != 'false'"  @click="displayIntervention(affaire.renseignerIntervention)" style="background-color: white; margin : 0; padding: 0; padding-bottom: 5px; padding-left: 5px;"><p style="font-size: 25px; color:green; font-wight: bold; font-weight: bold;">EP</p><i class="fa-solid fa-download" style="font-size: 25px; color:green;"></i></td>
                <td v-if="affaire.renseignerIntervention == 'false'" style="background-color: white; margin : 0; padding: 0; padding-bottom: 5px; padding-left: 5px;"><p style="font-size: 25px; color:red; font-wight: bold; font-weight: bold;">EP</p><i class="fa-solid fa-forward-step" style="font-size: 25px; color:red;"></i></td>

                <td v-if="affaire.importRapport != 'false'" style="background-color: white; margin : 0; padding: 0; padding-bottom: 5px; padding-left: 5px;"><p style="font-size: 25px; color:green; font-wight: bold;font-weight: bold;">RE</p><i class="fa-solid fa-clock" style="color: white"></i><i class="fa-solid fa-clock" style="color: white"></i></td>
                <td v-if="affaire.importRapport == 'false'"  @click="displayImportRapports(affaire.numeroAffaire)" style="background-color: white; margin : 0; padding: 0; padding-bottom: 5px; padding-left: 5px;"><p style="font-size: 25px; color:red; font-wight: bold; font-weight: bold;">RE</p></td>

                <td v-if="affaire.be != 'false'" style="background-color: white; margin : 0; padding: 0; padding-bottom: 5px; padding-left: 5px;" @click="showBE(affaire.be)"><p style="font-size: 25px; color:green; font-wight: bold;font-weight: bold;">BE</p><i class="fa-solid fa-download" style="font-size: 25px; color:green;"></i></td>
                <td v-if="affaire.be == 'false'" style="background-color: white; margin : 0; padding: 0; padding-bottom: 5px; padding-left: 5px;"><p style="font-size: 25px; color:red; font-wight: bold; font-weight: bold;">BE</p><i class="fa-solid fa-forward-step" style="font-size: 25px; color:white;"></i></td>

                <td v-if="affaire.facture != 'false'" style="background-color: white; margin : 0; padding: 0; padding-bottom: 5px; padding-left: 5px;" @click="showFacture(affaire.facture)"><p style="font-size: 25px; color:green; font-wight: bold;font-weight: bold;">FA</p><i class="fa-solid fa-download" style="font-size: 25px; color:green;"></i></td>
                <td v-if="affaire.facture == 'false'" style="background-color: white; margin : 0; padding: 0; padding-bottom: 5px; padding-left: 5px;"><p style="font-size: 25px; color:red; font-wight: bold; font-weight: bold;">FA</p><i class="fa-solid fa-forward-step" style="font-size: 25px; color:red;"></i></td>

                <td v-show="affaire.emise != 'false'" style="background-color: white; margin : 0; padding: 0; padding-bottom: 5px; padding-left: 5px;" @click="showEmise(affaire.emise)"><p style="font-size: 25px; color:green; font-wight: bold;font-weight: bold;">EN</p><i class="fa-solid fa-download" style="font-size: 25px; color:green;"></i></td>
                <td v-show="affaire.emise == 'false'" style="background-color: white; margin : 0; padding: 0; padding-bottom: 5px; padding-left: 5px;"><p style="font-size: 25px; color:red; font-wight: bold; font-weight: bold;">EN</p><i class="fa-solid fa-forward-step" style="font-size: 25px; color:red;"></i></td>

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
import Traitement from "../Affaire/Traitement.vue";

export default {
  name: "gestionInterlocuteur",
  components: {
    Traitement
  },
  data() {
    return {
      payments : [
        { type : "Chèque" },
        { type : "Virement" },
        { type : "Espèce" },
        { type : "Envoi" },
      ],
      typePaiement : null,
      flagUplodeCheque: false,
      flagPreviewUplodeCheque: false,
      flagUplodeEspeces: false,
      traitement :false,
      flagInsertEmise :false,
      arrayNumAffairesCloud : [],
      arrayNumAffairesLocal : [],
      numeroAffaire : null,
      flagImportFacture :false,
      flagMsgBonCommande : false,
      msgBonCommande : null,
      flagPreviewUplodeBonCammnade : false,
      file: null,
      affaireId : null,
      flagUplodeBonCammnade: false,
      rechercher: null,
      affaires : [],
      missions : [],
      clients : [],
      checkedClients : [],
      msg : null,
      flagEditClient : false,
      flagInfoClient : false,
      infoClient : [],
      succes : null,
      echec: null,
      refTransaction : null,
      targetIndex : null
    };
  },
  methods: {



    showEmise(filename) {

      Service.ShowEmise(filename)
        .then((data) => {
          console.log(data);
        })
        .catch((error) => {
          console.error(`HTTP error: ${error.name} => ${error.message}`);
          throw "fail request at: GET /refreshtime";
        });
    },

    deleteEmise(affaireId, nameFilePaiement, i) {


        Service.DeleteEmise(affaireId, nameFilePaiement)
        .then((result) => {
          if(result.msg == "succès") {
             this.affaires[i].emise = "false";
          }
        })
        .catch((err) => {
            console.log(err);
        });
    },

    enregitreCheque() {

        Service.EnregitreCheque(this.typePaiement, this.refTransaction, this.file, this.targetAffaire, this.targetIndex)
        .then((result) => {

          if(result.msg == "succès") {
            this.affaires[this.targetIndex].emise = true;
            this.flagInsertEmise = false;
          }

        })
        .catch((err) => {
            console.log(err);
        });

    },

    // preciew file
    previewFile() {
      this.file = this.$refs.file.files[0];
    },

    selectPaiement() {

      if(this.typePaiement == "chèque")
      {
          this.flagUplodeCheque = true;
      }

      if(this.typePaiement == "espèces")
      {
        this.flagUplodeEspeces= true;
      }


    },

    handelInsertEmise(affaireId, index) {
      this.targetAffaire = affaireId;
      this.targetIndex = index;
      this.flagInsertEmise = true;
    },

    downloadFile(response) {
        var newBlob = new Blob([response.data], {
            type: "application/vnd.openxmlformats-officedocument.wordprocessingml.document"
        });
        if (window.navigator && window.navigator.msSaveOrOpenBlob) {
            window.navigator.msSaveOrOpenBlob(newBlob);
            return;
        }
        const data = window.URL.createObjectURL(newBlob);
        var link = document.createElement("a");
        link.href = data;
        link.download = "resume.docx";
        link.click();
        setTimeout(function() {
            window.URL.revokeObjectURL(data);
        }, 100);
    },

    showFacture(facture){
      Service.showFACTURE(facture)
      .then((response) => {
        this.downloadFile(response);
      })
      .catch((err) => {
        alert(err);
      })

    },

    // handel Inser Facture
    handelInsertFacture(affaireId) {
      this.flagImportFacture = true;
      this.numeroAffaire = affaireId;
    },


    showBE(be) {
      Service.showBE(be)
      .then((response) => {
         this.downloadFile(response);
      })
      .catch((err) => {
        console.log(err)
      })

    },
    // handel Import BE
    handelImportBE(affaireId) {
        this.flagImportBD = true;
        this.numeroAffaire = affaireId;
    },
    // display Bon Cammande
    displayIntervention(filename) {
      console.log(filename);
      Service.displayIntervention(filename)
        .then((data) => {
          console.log(data);
        })
        .catch((error) => {
          console.error(`HTTP error: ${error.name} => ${error.message}`);
          throw "fail request at: GET /refreshtime";
        });
    },
  // display Bon Cammande
    displayBC(filename) {
      console.log(filename);
      Service.displayBC(filename)
        .then((data) => {
          console.log(data);
        })
        .catch((error) => {
          console.error(`HTTP error: ${error.name} => ${error.message}`);
          throw "fail request at: GET /refreshtime";
        });
    },
    // display DEVIS
    displayDEVIS(filename) {
      console.log(filename);
      Service.displayDEVIS(filename)
        .then((data) => {
          console.log(data);
        })
        .catch((error) => {
          console.error(`HTTP error: ${error.name} => ${error.message}`);
          throw "fail request at: GET /refreshtime";
        });
    },
// Enregistre Bon Cammande
enregitreBonCommande() {

        Service.enregitreBonCommande(this.file, this.affaireId)
        .then(() => {
            this.msgBonCommande = "Bon Cammande a été enregistré avec succès"
            this.flagMsgBonCommande = true;
            this.succes = true;
            this.echec = false;
            setTimeout(() => {
            this.msgBonCommande = ""
            this.flagMsgBonCommande = false;
            this.succes = null;
            this.echec = null;
            this.$router.go(this.$router.currentRoute);

            },5000)
        })
        .catch((error) => {

           this.msgBonCommande = error.message;
           this.flagMsgBonCommande = true;
           this.succes = false;
           this.echec = true;

            console.error(`HTTP error: ${error.name} => ${error.message}`);
            throw "fail request at: GET /refreshtime";
          });
},

// create bon cammnde
bonCommande(affaireId) {
    // prompt(message, defaultText, title, type, reverseButton)
    if(this.flagUplodeBonCammnade == true)
    {
      this.affaireId = null;
      this.flagUplodeBonCammnade = false

    } else {
      this.affaireId = affaireId;
      this.flagUplodeBonCammnade = true
    }
},
// delete more one client (clients)
    deleteClients() {
      const idClients = new Array();
      for(let i = 0; i < this.checkedClients.length; i++ )
      {
        idClients[i] = this.checkedClients[i];
        this.clients.splice(this.checkedClients[i], 1);

          Service.deleteClient(idClients[i])
          .then((result) => {
            this.msg = result.data.msg;
          })
          .catch((error) => {
              this.msg = error.message;
              console.error(`HTTP error: ${error.name} => ${error.message}`);
              throw "fail request at: GET /refreshtime";
          });
      }


    },
    // delete one client
    deleteClient(i) {
      const clientId = this.clients[i]._id;
      this.clients.splice(i, 1);
      Service.deleteClient(clientId)
      .then((result) => {

        this.msg = result.data.msg;
      })
      .catch((error) => {
          this.msg = error.message;
          console.error(`HTTP error: ${error.name} => ${error.message}`);
          throw "fail request at: GET /refreshtime";
      });
    },

    // edit one client
    editClient(i) {
      this.infoClient.push(this.clients[i])
      this.flagInfoClient = false;
      this.flagEditClient = true;
    },

    // edit one client
    informationClient(i) {
      this.infoClient.push(this.clients[i])
      this.flagEditClient = false;
      this.flagInfoClient = true;
    }
  },

 computed : {
      filterAffaires() {
            return this.affaires.filter((item) => {
              if(!this.rechercher)
              {
                return item
              }
                const date = new Date(item.date).toLocaleDateString()
                return !date.indexOf(this.rechercher) || !item.numeroAffaire.toString().indexOf(this.rechercher.toString())
            })
      }
  },

  created() {


 // read all Affaires LOCAL
      Service.readAllAffaires()
      .then((result) => {
         result.data.result.forEach((element) => {
           if(element.emise != 'false')
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
    padding: 5px;
}

.gestionInspecteur .rechercher-table table tr td {
    background-color: #ddd;
    color: black;
    padding: 5px;
}
.gestionInspecteur .rechercher-table table tr td {
  cursor: pointer;
}

.gestionInspecteur .rechercher-table table tr td a {
  cursor: pointer;
  margin-left: 10px;
  margin-right: 10px;
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
 margin-left: 5px;
 cursor: pointer;
 font-size: 18px;
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


h4 {
  width: fit-content;
  height: fit-content;
  padding: 5px;
  margin: 0;
  color: white;
  background-color: #243064;
  text-align: center;
  margin-bottom: 10px;
}

#inspecteurs > tr > td:nth-child(7) > a > svg {
  font-size: 25px;
  color: red;
}


#app > div > div > div.menu-content > div.content > div > div > div.uplode {
    width: 100%;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: center;
    margin-bottom: 10px;
}

#app > div > div > div.menu-content > div.content > div > div > div.uplode > h4 {
    width: 400px;
    padding: 10px;
}

#app > div > div > div.menu-content > div.content > div > div > div.uplode > div:nth-child(2) {
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: flex-start;
    width: 400px;
}

#app > div > div > div.menu-content > div.content > div > div > div.uplode > div:nth-child(2) > label {
  margin-bottom: 5px;
  margin-top: 5px;
}

#app > div > div > div.menu-content > div.content > div > div > div.uplode > div:nth-child(2) > select {
  height: 40px;
  width: 100%;
}

#app > div > div > div.menu-content > div.content > div > div > div.uplode > div:nth-child(3) {
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: flex-start;
    width: 400px;
}


#app > div > div > div.menu-content > div.content > div > div > div.uplode > div:nth-child(3) > label {
  margin-bottom: 5px;
  margin-top: 5px;
}

#app > div > div > div.menu-content > div.content > div > div > div.uplode > div:nth-child(3) > input {
  height: 40px;
  width: 100%;
}

#app > div > div > div.menu-content > div.content > div > div > div.uplode > div:nth-child(4) {
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: flex-start;
    width: 400px;
}


#app > div > div > div.menu-content > div.content > div > div > div.uplode > div:nth-child(4) > label {
  margin-bottom: 5px;
  margin-top: 5px;
}

#app > div > div > div.menu-content > div.content > div > div > div.uplode > div:nth-child(4) > input {
  height: 40px;
  width: 100%;
}

#app > div > div > div.menu-content > div.content > div > div > div.uplode > div:nth-child(6) {
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: flex-start;
    width: 400px;
}


#app > div > div > div.menu-content > div.content > div > div > div.uplode > div:nth-child(6) > label {
  margin-bottom: 5px;
  margin-top: 5px;
}

#app > div > div > div.menu-content > div.content > div > div > div.uplode > div:nth-child(6) > input {
  height: 40px;
  width: 100%;
  color:  white;
  border: 0px;
  cursor: pointer;
  background-color: green;
}

#app > div > div > div.menu-content > div.content > div {
  background-color: white;
}

#inspecteurs > tr > td {
  background-color: white;
  border-bottom: 1px solid #243064;
}

#app > div > div > div.menu-content > div.content > div > h3 {

    width: 100%;
    height: -webkit-fit-content;
    height: -moz-fit-content;
    height: fit-content;
    margin:0;

    color: white;
    background: linear-gradient(346deg, rgba(207,31,33,1) 0%, rgba(24,86,161,1) 100%);    text-align: center;
    margin-bottom: 10px;
    padding: 10px;
    font-size: 25px;

}



</style>