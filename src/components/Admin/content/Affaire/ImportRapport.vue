<template>
   <div class="indicateur">
<!-- Start etap Action Rapport -->
        <div class="actionRapport">
              <div class="diviser">
                    <input type="submit" value="Étape 1 - Import des rapport" @click="importRapports()">
              </div>

              <div class="diviser">
                    <input type="submit" value="Étape 2 - Classfication Affaire" @click="classfication()">
              </div>

              <div class="diviser">
                    <input type="submit" value="Étape 3 - classe par Rapport" @click="classficationRapport()">
              </div>

              <div class="diviser">
                    <input type="submit" value="Étape 4 - classe Shema" @click="classficationShema()">
              </div>

              <div class="diviser">
                    <input type="submit" value="Étape 5 - Envoyer des rapport" @click="envoyerRapports()">
              </div>

              <div class="diviser">
                    <input type="submit" value="Étape 6 - Terminer des rapport" @click="accepterRapport()">
              </div>
        </div>
<!-- End etap action rapport         -->


<!-- Start  Import Rapport   -->
        <div class="list Mission" v-if="importRapport">
          <p> <i class="fa-solid fa-circle-exclamation"></i>Attention lors de la saisie des rapports, vous devez prendre en compte l'ordre "Référence de rapport" de chaque rapport séparément</p>

          <table v-for="(mission, j) in filterMissions" :key="mission._id" border="1">

            <thead>
              <tr>
                <th colspan="100">  {{ mission.typeMission }} </th>
              </tr>
            </thead>

            <tbody>

              <tr>
                <td>Equipement :</td>
                <td v-for="(eq, i) in mission.equipement" :key="i">
                    {{ eq.titre }}
                </td>
              </tr>

              <tr>
                <td>Quantité :</td>
                <td v-for="(eq, i) in mission.qte" :key="i">
                  <ul>
                    <li>{{ eq }}</li>
                    <li><button type="submit" @click="addQuantite(j, i)">Ajouter</button></li>
                  </ul>
                </td>
              </tr>

              <tr>
                <td>References Rapports :</td>
                <td v-for="(eq, i) in mission.equipement" :key="i">
                  <ul v-for="(item, index) in eq.refRapport" :key="item">
                    <li>
                      <label></label>
                      <input type="hidden" v-model="mission.typeMission" :key="index" disabled>
                    </li>

                    <li>
                      <label>Type Rapport</label>
                      <input type="text" v-model="mission.typeRapport" :key="index" disabled>
                    </li>

                    <li>
                      <label> Catégorie</label>
                      <input type="text" v-model="mission.categorie" :key="index" disabled>
                    </li>

                    <li>
                      <label></label>
                      <input type="hidden" v-model="clientId" :key="index" disabled>
                    </li>

                    <li v-if="!eq.sendRapport[index]">
                      <label>Reference Rapport :</label>
                      <input type="text" v-model="eq.refRapport[index]" disabled>
                    </li>

                    <li v-if="!eq.sendRapport[index]">
                      <label>Désignation :</label>
                      <input type="text" @input="event => designation = event.target.value">
                    </li>

                    <li v-if="!eq.sendRapport[index]">
                      <label>Date Production Contrôle :</label>
                      <input type="date" @input="event => dateProductionControle = event.target.value">
                    </li>

                    <li v-if="!eq.sendRapport[index]">
                      <label>Date d'intervention :</label>
                      <input type="date" @input="event => dateIntervention = event.target.value">
                    </li>

                    <li v-if="!eq.sendRapport[index]">
                      <label for="cars">Responsable Client</label>
                      <input type="text" @input="event => responsableClient = event.target.value">
                    </li>

                    <li v-if="!eq.sendRapport[index]">
                      <label>Rapport file :</label>
                      <input type="file" multiple="multiple" placeholder="Fichier PDF" ref="file" @change="onFileChange($event, i)">
                    </li>

                    <li v-if="!eq.sendRapport[index]">
                      <input type="button" value="Valider" @click="saveRapport(eq.refRapport[index], mission.typeMission, i, j, index, mission._id, mission.typeRapport, mission.categorie)">
                    </li>

                    <li class="modifier" v-if="eq.sendRapport[index]">
                      <input type="text" v-model="eq.refRapport[index]" disabled>
                      <input type="button" value="Supprimer" @click="modifier(eq.refRapport[index], mission._id, mission.numeroAffaire, i, j, index)">
                    </li>

                    <hr>
                  </ul>
                </td>
              </tr>

            </tbody>

          </table>

        </div>

        <!-- End Import Rapport -->

<!-- Start Classment Rapport -->
        <div class="list Mission" v-if="classementRapport">
          <p v-if="buttonClassficationAffaire == false"> Vous Pouvez sisair un classfication a cet affaire<input type="button" value="Oui" @click="handelClassficationOne"></p>
          <p> {{ message }}</p>


          <!-- Start One -->
          <div v-if="classficationAffaire">
                <p>Première classe - 1</p>
                <div v-for="itemOne in niveauOne" :key="itemOne.count">
                    <input type="text" v-model="itemOne.titre" :disabled = itemOne.disabled>
                </div>
                <br>
              <input type="button" value="add" v-if="niveauOne[0].disabled == false" @click="addNiveauOne()">
              <input type="button" value="save" v-if="niveauOne[0].disabled == false" @click="saveNiveauOne()">
              <input type="button" value="delete" v-if="niveauOne[0].disabled == true" @click="deleteNiveauOne()">
          </div>



          <!-- End One -->


          <!-- Start Tow -->
          <div v-if="classficationAffaire">
              <p>Deuxième classe - 2</p>
              <div v-for="itemTow in niveauTow" :key="itemTow.count">
                  <input type="text" v-model="itemTow.titre" :disabled = itemTow.disabled>
              </div>
            <input type="button" value="add" v-if="niveauTow[0].disabled == false" @click="addNiveauTow()">
            <input type="button" value="save" v-if="niveauTow[0].disabled == false" @click="saveNiveauTow()">
            <input type="button" value="delete" v-if="niveauTow[0].disabled == true" @click="deleteNiveauTow()">
          </div><br>

          <!-- End Tow -->


          <!-- Start Tree -->
          <div v-if="classficationAffaire">
            <p>Troisième classe - 3</p>
            <div v-for="itemTree in niveauTree" :key="itemTree.count">
                <input type="text" v-model="itemTree.titre" :disabled = itemTree.disabled>
            </div>
            <input type="button" value="add" v-if="niveauTree[0].disabled == false" @click="addNiveauTree()">
            <input type="button" value="save" v-if="niveauTree[0].disabled == false" @click="saveNiveauTree()">
            <input type="button" value="delete" v-if="niveauTree[0].disabled == true" @click="deleteNiveauTree()">
          </div><br>
          <!-- End Tree -->

          <!-- Start Four -->
          <div v-if="classficationAffaire">
            <p>Troisième classe - 4</p>
            <div v-for="itemFour in niveauFour" :key="itemFour.count">
                <input type="text" v-model="itemFour.titre" :disabled = itemFour.disabled>
            </div>
            <input type="button" value="add" v-if="niveauFour[0].disabled == false" @click="addNiveauFour()">
            <input type="button" value="save" v-if="niveauFour[0].disabled == false" @click="saveNiveauFour()">
            <input type="button" value="delete" v-if="niveauFour[0].disabled == true" @click="deleteNiveauFour()">
          </div><br>
          <!-- End Four -->

          <!-- Start Five -->
          <div v-if="classficationAffaire">
            <p>Troisième classe - 5


            </p>
            <div v-for="itemFive in niveauFive" :key="itemFive.count">
                <input type="text" v-model="itemFive.titre" :disabled = itemFive.disabled>
            </div>
            <input type="button" value="add" v-if="niveauFive[0].disabled == false" @click="addNiveauFive()">
            <input type="button" value="save" v-if="niveauFive[0].disabled == false" @click="saveNiveauFive()">
            <input type="button" value="delete" v-if="niveauFive[0].disabled == true" @click="deleteNiveauFive()">
          </div><br>
          <!-- End Five -->

          <!-- Start Input Enregistre classfication -->
          <input type="button" value="Enregistrer et terminer" v-if="classficationAffaire && buttonClassficationAffaire == false" @click="enregistrerClassAffaire()">
          <!-- End Input Enregistre classfication -->

          <!-- Start Input delete classfication -->
          <input type="button" value="Effacer" v-if="buttonClassficationAffaire == true" @click="effacerClassAffaire()">
          <!-- End Input delete classfication -->
        </div>
          <!-- End  Classment Rapporrts -->

          <!-- Start  List Rapports -->
          <div v-if="classficationRapports">
            <ul v-for="(rapport, index) in filterRapports" :key="rapport._id">
              <li>
                <label for="">Rapport {{ rapport.disabled }}</label>
                <input type="text" :value="rapport.referenceRapport" disabled>

                <label for="">Class - 1</label>
                <select v-model="rapport.niveauUn" :disabled = rapport.disabled>
                  <option v-for="niveau in niveauOne" :key="niveau.count" >{{ niveau.titre }}</option>
                 </select>

                 <label for=""> Class - 2</label>
                <select v-model="rapport.niveauDeux" :disabled = rapport.disabled>
                  <option v-for="niveau in niveauTow" :key="niveau.count" >{{ niveau.titre }}</option>
                 </select>

                <label for=""> Class - 3</label>
                <select v-model="rapport.niveauTrois" :disabled = rapport.disabled>
                  <option v-for="niveau in niveauTree" :key="niveau.count" >{{ niveau.titre }}</option>
                </select>

                <label for=""> Class - 4</label>
                <select v-model="rapport.niveauQuatre" :disabled = rapport.disabled>
                  <option v-for="niveau in niveauFour" :key="niveau.count" >{{ niveau.titre }}</option>
                </select>

                <label for=""> Class - 5</label>
                <select v-model="rapport.niveauCinq" :disabled = rapport.disabled>
                  <option v-for="niveau in niveauFive" :key="niveau.count" >{{ niveau.titre }}</option>
                </select>

                 <input v-if="rapport.disabled == false" type="button" value="Enregistrer" @click="enregistrerClassRapport(rapport._id, rapport.niveauUn, rapport.niveauDeux, rapport.niveauTrois, rapport.niveauQuatre, rapport.niveauCinq, index)">
                 <input v-if="rapport.disabled == true" type="button" value="Supprimer" @click="deleteClassRapport(rapport._id, index)">
              </li>
            </ul>
          </div>
          <!-- Start  List Rapports -->


        <!-- Start Classfication Shema -->
        <div v-if="flagClassficationShema">
          <ul v-for="(item, index) in schemaClass" :key="item._id">
            <li>
                <select v-model="item.niveauOne" :disabled = item.disabled>
                  <option v-for="niveau in niveauOne" :key="niveau.count" >{{ niveau.titre }}</option>
                </select>
                <select v-model="item.niveauTow" :disabled = item.disabled>
                  <option v-for="niveau in niveauTow" :key="niveau.count" >{{ niveau.titre }}</option>
                </select>
                <select v-model="item.niveauTree" :disabled = item.disabled>
                  <option v-for="niveau in niveauTree" :key="niveau.count" >{{ niveau.titre }}</option>
                </select>

                <select v-model="item.niveauFour" :disabled = item.disabled>
                  <option v-for="niveau in niveauFour" :key="niveau.count" >{{ niveau.titre }}</option>
                </select>

                <select v-model="item.niveauFive" :disabled = item.disabled>
                  <option v-for="niveau in niveauFive" :key="niveau.count" >{{ niveau.titre }}</option>
                </select>
                <input v-if="item.disabled == false" type="button" value="Enregistrer" @click="enregistrerClassShema(item.niveauOne, item.niveauTow, item.niveauTree, item.niveauFour, item.niveauFive, index)">
                 <input v-if="item.disabled == true" type="button" value="Supprimer" @click="deleteClassShema(index)">
            </li>
          </ul>
          <input type="button" value="Ajouter un Schema" @click="ajouterShema()">
        </div>
        <!-- End Classfication Shema  -->

        <!-- Start Envoye Rapport -->
        <div v-if="envoyerRapport">
          <div>
            <ul>
              <li>Sélectionnez le destinataire par Email</li>
              <li v-for="item in interlocuteurs" :key="item.email">
                {{ item.nom+","+item.prenom }}
                <input type="checkbox" :value="item.email" v-model="checkedInterlocuteur">
              </li>
            </ul>
          </div>
          <div>
            <div>
              <p>Les rapports que vous souhaitez envoyer et valider</p>
              <ul v-for="item in rapports" :key="item.referenceRapport">
                    <input type="checkbox" :checked="item.sendClient === true" @change="onChangeProcessed($event, item.referenceRapport)">{{ item.referenceRapport }} -  {{ item.designation }}
              </ul>
            </div>
          </div>
          <input type="button" value="Envoyer au client et valider" @click="envoyerClient()">
          <p> {{ msgValidationRapport }}</p>
        </div>
        <!-- End Envoye Rapport  -->






    </div>
</template>

<script>
import Service from "../../../../Service";
import Rapport from "../../../../Rapport";


export default {

    props: {
      numeroAffaire: String,
    },

    components: {

    },

    data() {
        return {

            msgSendClient : null,
            interlocuteurs : [],
            checkedInterlocuteur : [],
            checkedRapport : [],
            envoyerRapport : false,
            importRapport : true,
            classementRapport : false,
            msgRapport : null,
            traitement: false,
            missions : [],
            numRapport: [],
            countRapport : [],
            clientId: null,
            referenceRapport : null,
            designation: null,
            dateProductionControle: null,
            dateIntervention :null,
            responsableClient : null,
            typeRapport : null,
            category: null,
            selectedFile: [],
            refRapport : null,
            newRefRapport :null,
            arber :  [],
            rapports : [],
            affaire: null,
            niveauOne : [],
            niveauUn : [],
            niveauTow : [],
            niveauDeux : [],
            niveauTree : [],
            niveauTrois : [],
            niveauFour : [],
            niveauQuatre : [],
            niveauFive : [],
            niveauCinq : [],
            classficationAffaire : false,
            classficationRapports : false,
            flagClassficationShema : false,
            message : null,
            buttonClassficationAffaire : false,
            rapportsInterlocuteurs : [],
            schemaClass : [],
            msgValidationRapport : null
        }
    },

    methods : {

    ajouterShema() {

      this.schemaClass.push({
        niveauOne : null,
        niveauTow : null,
        niveauTree : null,
        niveauFour : null,
        niveauFive : null,
        disabled : false
      });
    },

    enregistrerClassShema(niveauUn, niveauDeux, niveauTrois, niveauQuatre, niveauCinq, index) {
          Service.EnregistrerClassShema(this.numeroAffaire, niveauUn, niveauDeux, niveauTrois, niveauQuatre, niveauCinq, index)
          .then((result) => {
            if(result) {
              this.schemaClass[index].disabled = true;
            }
          })
          .catch((error) => {
            console.log(error.message);
          });
    },

    deleteClassShema(index) {
          Service.DeleteClassShema(this.numeroAffaire, index)
          .then((result) => {
            if(result) {
              this.schemaClass[index].disabled = false;
            }
          })
          .catch((error) => {
            console.log(error.message);
          });
    },

    enregistrerClassRapport(rapportId, niveauUn, niveauDeux, niveauTrois, niveauQuatre, niveauCinq, index) {
          Service.EnregistrerClassRapport(rapportId, niveauUn, niveauDeux, niveauTrois, niveauQuatre, niveauCinq)
          .then((result) => {
            if(result) {
              this.rapports[index].disabled = true
            }
          })
          .catch((error) => {
            console.log(error.message);
          });
    },

    deleteClassRapport(rapportId, index) {
          Service.DeleteClassRapport(rapportId)
          .then((result) => {
            if(result) {
              this.rapports[index].disabled = false
            }
          })
          .catch((error) => {
            console.log(error.message);
          });
    },

    // one
    saveNiveauOne() {

        for(let i = 0; i < this.niveauOne.length; i++) {
               this.niveauOne[i].disabled = true
        }

    },

    addNiveauOne() {

        this.niveauOne.push({
              count : this.niveauOne.length,
              titre : null,
              disabled : false
        });
    },

    deleteNiveauOne() {

        for(let i = 0; i < this.niveauOne.length; i++) {
               this.niveauOne[i].disabled = false
        }

    },
// one


// tow
    saveNiveauTow() {

        for(let i = 0; i < this.niveauTow.length; i++) {
               this.niveauTow[i].disabled = true
        }

    },


    deleteNiveauTow() {

        for(let i = 0; i < this.niveauTow.length; i++) {
               this.niveauTow[i].disabled = false
        }

    },

    addNiveauTow() {

        this.niveauTow.push({
              count : this.niveauTow.length,
              titre : null,
              disabled : false
        });
    },

// tow


// tree

    saveNiveauTree() {

        for(let i = 0; i < this.niveauTree.length; i++) {
               this.niveauTree[i].disabled = true
        }

    },

    deleteNiveauTree() {

        for(let i = 0; i < this.niveauTree.length; i++) {
               this.niveauTree[i].disabled = false
        }

    },

    addNiveauTree() {

        this.niveauTree.push({
              count : this.niveauTree.length,
              titre : null,
              disabled : false
        });
    },
// tree

// four

    saveNiveauFour() {

        for(let i = 0; i < this.niveauFour.length; i++) {
               this.niveauFour[i].disabled = true
        }

    },

    deleteNiveauFour() {

        for(let i = 0; i < this.niveauFour.length; i++) {
               this.niveauFour[i].disabled = false
        }

    },

    addNiveauFour() {

        this.niveauFour.push({
              count : this.niveauFour.length,
              titre : null,
              disabled : false
        });
    },
// four

// Five

    saveNiveauFive() {

        for(let i = 0; i < this.niveauFive.length; i++) {
               this.niveauFive[i].disabled = true
        }

    },

    deleteNiveauFive() {

        for(let i = 0; i < this.niveauFive.length; i++) {
               this.niveauFive[i].disabled = false
        }

    },

    addNiveauFive() {

        this.niveauFive.push({
              count : this.niveauFive.length,
              titre : null,
              disabled : false
        });
    },
// Five


    enregistrerClassAffaire() {

        for(let i = 0; i < this.niveauOne.length; i++) {
               this.niveauUn.push(this.niveauOne[i].titre);
        }

        for(let i = 0; i < this.niveauTow.length; i++) {
               this.niveauDeux.push(this.niveauTow[i].titre);
        }

        for(let i = 0; i < this.niveauTree.length; i++) {
               this.niveauTrois.push(this.niveauTree[i].titre);
        }

        for(let i = 0; i < this.niveauFour.length; i++) {
               this.niveauQuatre.push(this.niveauFour[i].titre);
        }

        for(let i = 0; i < this.niveauFive.length; i++) {
               this.niveauCinq.push(this.niveauFive[i].titre);
        }

      Service.EnregistrerClassAffaire(this.numeroAffaire, this.niveauUn, this.niveauDeux, this.niveauTrois, this.niveauQuatre, this.niveauCinq)
      .then((result) => {
          this.buttonClassficationAffaire = result.msg.saved;
          return this.$router.go(this.$router.currentRoute);

      })
      .catch((error) => {
          console.log(error);
      });

    },

    effacerClassAffaire() {
        Service.EffacerClassAffaire(this.numeroAffaire)
        .then(() => {
          return this.$router.go(this.$router.currentRoute);
        })
        .catch((error) => {
            console.log(error);
        });
    },

    handelClassficationOne() {

        this.niveauOne.push({
              count : this.niveauOne.length,
              titre : null,
              disabled : false
        });

        this.niveauTow.push({
              count : this.niveauTow.length,
              titre : null,
              disabled : false
        });

        this.niveauTree.push({
              count : this.niveauTree.length,
              titre : null,
              disabled : false
        });

        this.niveauFour.push({
              count : this.niveauFour.length,
              titre : null,
              disabled : false
        });

        this.niveauFive.push({
              count : this.niveauFive.length,
              titre : null,
              disabled : false
        });

        this.classficationAffaire = true;
    },

    onChangeProcessed(e,d) {

          if (e.target.checked == true) {

            if(!this.checkedRapport.includes(d))
            {
              this.checkedRapport.push(d);
            }

          }

          if (e.target.checked == false ) {
              const index = this.checkedRapport.indexOf(d);

              if(index != -1) {
                this.checkedRapport.splice(index, 1);

              }
          }

    },

    envoyerClient() {

      Service.EnvoyerClientEmail(this.checkedInterlocuteur, this.checkedRapport, this.numeroAffaire)
      .then((res) => {
          if(res) {
                this.msgValidationRapport = res.data.msg
          }
      })
      .catch((error) => {
          console.log(error);
      });

    },

    classficationShema() {
        this.flagClassficationShema = true;
        this.classficationRapports = false;
        this.importRapport = false;
        this.classementRapport = false;
        this.envoyerRapport = false;
    },

   classficationRapport() {
        this.flagClassficationShema = false;
        this.classficationRapports = true;
        this.importRapport = false;
        this.classementRapport = false;
        this.envoyerRapport = false;
   },

    classfication() {

        this.flagClassficationShema = false;
        this.classficationRapports = false;
        this.importRapport = false;
        this.classementRapport = true;
        this.envoyerRapport = false;

    },

    importRapports() {

        this.flagClassficationShema = false;
        this.classficationRapports = false;
        this.importRapport = true;
        this.classementRapport = false;
        this.envoyerRapport = false;

    },

    envoyerRapports() {

      this.rapports.forEach((element) => {
          if(element.sendClient == true) {
            this.checkedRapport.push(element.referenceRapport);
          }
      });

        this.flagClassficationShema = false;
        this.classficationRapports = false;
        this.importRapport = false;
        this.classementRapport = false;
        this.envoyerRapport = true;

    },

    modifier(refRapport, missionId, numeroAffaire, i, j, index) {

        Service.ModifierRapport(refRapport, missionId, numeroAffaire, i, j, index)
        .then((result) => {
            if(result)  {
                this.missions[j].equipement[i].sendRapport.splice(index, 1);
                this.missions[j].equipement[i].sendRapport.splice(index, 1, false);
            }

        })
        .catch((error) => {
            console.log(error);
        });
    },

    checkedRapports() {
      console.log(this.checkedNames, this.checkedRapportIndex);
    },

    onFileChange(e, i) {
      const selectedFile = e.target.files[0]; // accessing file
      this.selectedFile[i] = selectedFile;
    },

        // add Quantite Rapport
    addQuantite(b, c) {

    var today = new Date();
    var mm = String(today.getMonth() + 1).padStart(2, '0');
    var yyyy = today.getFullYear();

      if(this.newRefRapport == null) {
        Service.getLastRapport()
        .then((res) => {

              // Get last rapport
              this.refRapport = res.last[0].LastRapport;
              // get counter for increment rapport
              const count = this.refRapport.slice(
                    this.refRapport.indexOf('A') + 1,
                    this.refRapport.lastIndexOf('|'),
              );
              //get titre societe
              const titre = this.numeroAffaire.slice(
                  this.numeroAffaire.lastIndexOf('|') + 1,
              );

              this.newRefRapport = `RA${parseInt(count) + 1}|${mm}|${yyyy}|${titre}`
              // set in array missions
              this.missions[b].equipement[c].refRapport.push(this.newRefRapport);

        })
        .catch((error) => {
          console.log(error);
        });

      }else {
              // Get last rapport
              this.refRapport = this.newRefRapport;
              // get counter for increment rapport
              const count = this.refRapport.slice(
                    this.refRapport.indexOf('A') + 1,
                    this.refRapport.lastIndexOf('|'),
              );
              //get titre societe
              const titre = this.numeroAffaire.slice(
                  this.numeroAffaire.lastIndexOf('|') + 1,
              );

              this.newRefRapport = `RA${parseInt(count) + 1}|${mm}|${yyyy}|${titre}`
              // set in array missions
              this.missions[b].equipement[c].refRapport.push(this.newRefRapport);
      }



        // Start add Quantite Equipment
        const oldQte = this.missions[b].qte[c];
        const newQte = parseInt(oldQte) + 1;
        this.missions[b].qte.splice(b, 1, newQte);
        // Fin add Quantite Equipement

      },



      // valider Rapport
      saveRapport(referenceRapport, typeMission, i, j, index, missionId, typeRapport, categorie) {

        Rapport.SaveRapport(this.selectedFile[i], this.clientId, referenceRapport, typeMission, this.designation, this.dateProductionControle, this.dateIntervention, this.responsableClient, missionId, i, j, index, this.numeroAffaire, typeRapport, categorie)
          .then((result) => {

            if(result)  {
                this.missions[j].equipement[i].sendRapport.splice(index, 1);
                this.missions[j].equipement[i].sendRapport.splice(index, 1, true);
            }

          })
          .catch((error) => {
               console.log(error);
          });

      },

      accepterRapport() {
        Service.accepterRapport(this.numeroAffaire)
        .then(() => {
              this.msgRapport = "Veuillez patienter quelques secondes pour terminer le processus.";
              this.traitement = true;
              setTimeout(() => {
                  return this.$router.go(this.$router.currentRoute);
              }, 5000)

        })
        .catch((err) => {
          console.log(err)
        });
      }

    },

    created() {


      // check classfication exist or no
      Service.ReadClassAffaire(this.numeroAffaire)
      .then((result) => {

          result.msg.classOne.forEach((element, index) => {
            this.niveauOne.push({
              count : index,
              titre : element,
              disabled : true
            });
          });

          result.msg.classTow.forEach((element, index) => {
            this.niveauTow.push({
              count : index,
              titre : element,
              disabled : true
            });
          });

          result.msg.classTree.forEach((element, index) => {
            this.niveauTree.push({
              count : index,
              titre : element,
              disabled : true
            });
          });

          result.msg.classFour.forEach((element, index) => {
            this.niveauFour.push({
              count : index,
              titre : element,
              disabled : true
            });
          });

          result.msg.classFive.forEach((element, index) => {
            this.niveauFive.push({
              count : index,
              titre : element,
              disabled : true
            });
          });

          this.schemaClass = result.msg.schemaClass;

          // check schema exist or no by numero d affaire
          if(this.schemaClass.length > 1) {
            this.schemaClass.shift();
          }

          if(result) {
              this.buttonClassficationAffaire = true;
              this.classficationAffaire = true;
          }

      })
      .catch((error) => {
          console.log(error);
      });


      // Get info missions by affaire
      Service.getMission(this.numeroAffaire)
        .then((result) => {

          this.clientId = result.data.missions[0].clientId

            // sommier  les valeur array pour justifie counteur rapport
            result.data.missions.forEach(element => {
              this.missions.push(element);
                  element.qte.forEach((el) => {
                      this.countRapport.push(parseInt(el));
                  })
            });


        })
        .catch((err) => {
            console.log(err);
        });


      // Get info rapports by numeroAffaire
      Service.selectRapports(this.numeroAffaire)
        .then((result) => {
            this.rapports = result.rapports;
        })
        .catch((err) => {
            console.log(err);
        });

      // Get Info Client by numeroAffaire and get Les Interlocuteurs
      Service.getRefClientByNumeroAffaire(this.numeroAffaire)
      .then((result) => {

          this.interlocuteurs = result.data.interlocuteurs;

          this.rapports.forEach((element) => {
              element.interlocuteurs = this.interlocuteurs;
          });

      })
      .catch((error) => {
          console.log(error);
      });

    },

    computed : {

      filterMissions() {
        return this.missions.filter((item) => {
            return item;
        });
      },

      filterRapports() {
        return this.rapports.filter((item) => {
          return item.sendClient == false;
        });
      }
    }

}


</script>

<style scoped>



.form {
  width: 100%;
  display :flex;
  flex-direction: row;
  flex-wrap:wrap;
  justify-content:flex start ;
  align-items: flex-start;
}

.form div {
  width: 45%;
  display:flex;
  flex-direction: column;
  justify-content:flex-start;
  margin-left: 10px;
  align-items: flex-start;
}

 .form div input {
  height: 40px;
  margin-bottom: 5px;
  border: 1px solid #243064;
  width:100%;

}

.form div select {
  height: 40px;
    width:100%;

}

hr {
  width: 100%;
  background-color: black;
  height: 10px;
}

.ajouter {
  background-color: rgb(0, 0, 247);
  color: white;
  border: 0px;
  outline: 0px;
  cursor: pointer;
}

.valider {
  background-color: green;
  color: white;
  border: 0px;
  outline: 0px;
  cursor: pointer;
}
.annuler {
  background-color: red;
  color: white;
  border: 0px;
  outline: 0px;
  cursor: pointer;
}
.apercu {
  background-color: orange;
  color: white;
  border: 0px;
  outline: 0px;
  cursor: pointer;
}
.enregistrer {

  background-color: green;
  color: white;
  border: 0px;
  outline: 0px;
  cursor: pointer;
}

.traitement{
    height: fit-content;
    background-color: #f3f3f3;
    width: 100%;
    display: flex;
    flex-direction: row;
    justify-content: flex-start;
    align-items: center;
}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > table > tbody > tr > td {
  text-align :center;
  font-size: bold;
  color: black;
}

#app > div > div > div.menu-content > div.content > div > div > div.deleteAll > input[type=submit] {

    background-color: #3abf3ab3;
    border: 0;
    color: #010c03;
    padding: 15px;
    text-align: center;
    border-radius: 10px;
    margin-top: 10px;
    cursor: pointer;

}


#app > div > div > div.menu-content > div.content > div > div > div.deleteAll  {

  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;

}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > table > tbody > tr > td:nth-child(2) > ul {
  display: flex;
  flex-direction: column;
  width: 100%;
}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > table > tbody > tr > td:nth-child(2) > ul li {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > table > tbody > tr > td > ul li label {
 font-size: 18px;
 margin-bottom: 5px;
}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > table > tbody > tr > td > ul li input {
 height: 40px;
 width: 95%;
}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > table > tbody > tr > td > ul li select {
 height: 40px;
  width: 95%;

}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > table:nth-child(1) > tbody > tr > td > ul:nth-child(1) {
  display: flex;
  flex-direction: column;
}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > table > tbody > tr > td > ul > li:nth-child(10) > input[type=button] {
    background: green;
    border: 0;
    color: white;
    font-size: 15px;
    cursor: pointer;
}

.modifier input[type=button] {
  background-color: red;
  border: 0;
  color: white;
  cursor: pointer;
}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > p {
  color: red;
  font-size: 20px;
}

#app > div > div > div.menu-content > div.content > div > div > div.actionRapport {
  display: flex;
  flex-direction: row;
}

#app > div > div > div.menu-content > div.content > div > div > div.actionRapport > div:nth-child(1) > input[type=submit] {
    margin-top: 20px;
    padding: 10px;
    color: white;
    background-color: #4908f7ba;
    border: 0;
    font-size: 16px;
    cursor: pointer;
    border-radius: 5px;
}

#app > div > div > div.menu-content > div.content > div > div > div.actionRapport > div:nth-child(2) > input[type=submit] {
    margin-top: 20px;
    margin-left: 20px;
    padding: 10px;
    color: white;
    background-color: #4908f7ba;
    border: 0;
    font-size: 16px;
    cursor: pointer;
    border-radius: 5px;
}


#app > div > div > div.menu-content > div.content > div > div > div.actionRapport > div:nth-child(3) > input[type=submit] {
    margin-top: 20px;
    margin-left: 20px;
    padding: 10px;
    color: white;
    background-color: #4908f7ba;
    border: 0;
    font-size: 16px;
    cursor: pointer;
    border-radius: 5px;
}
#app > div > div > div.menu-content > div.content > div > div > div.actionRapport > div:nth-child(4) > input[type=submit] {
    margin-top: 20px;
    margin-left: 20px;
    padding: 10px;
    color: white;
    background-color: #4908f7ba;
    border: 0;
    font-size: 16px;
    cursor: pointer;
    border-radius: 5px;
}
#app > div > div > div.menu-content > div.content > div > div > div.actionRapport > div:nth-child(5) > input[type=submit] {
    margin-top: 20px;
    margin-left: 20px;
    padding: 10px;
    color: white;
    background-color: #4908f7ba;
    border: 0;
    font-size: 16px;
    cursor: pointer;
    border-radius: 5px;
}

#app > div > div > div.menu-content > div.content > div > div > div.actionRapport > div:nth-child(6) > input[type=submit] {
    margin-top: 20px;
    margin-left: 20px;
    padding: 10px;
    color: white;
    background-color: green;
    border: 0;
    font-size: 16px;
    cursor: pointer;
    border-radius: 5px;
}


#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > li  {
  list-style: none;
}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > ul > li > ul {
  margin-left: 50px;
}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > ul > ul > div {
margin-left: 50px;
}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > ul > ul > div > ul > div {
margin-left: 50px;
}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > ul > ul > div > ul > div > ul > div {
margin-left: 50px;
}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > ul > ul > div > ul > div > ul > div > ul > div {
margin-left: 50px;
}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > ul > ul > ul {
  margin-left: 30px;
}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > input[type=button] {
  background-color: green;
  color: white;
  margin-top: 20px;
  padding: 15px;
  border: 0px;
  cursor: pointer;
  border-radius: 5px;
}

#app > div > div > div.menu-content > div.content > div > div > div:nth-child(1) > div:nth-child(1) > ul > li:nth-child(1) {
    width: 100%;
    background-color: red;
    color: white;
    text-align: center;
    padding: 10px;
    font-size: 18px;
    margin-top: 20px;
    margin-bottom: 20px;
}

#app > div > div > div.menu-content > div.content > div > div > div:nth-child(1) > div:nth-child(2) > ul > li:nth-child(1) {
    width: 100%;
    background-color: red;
    color: white;
    text-align: center;
    padding: 10px;
    font-size: 18px;
    margin-top: 20px;
    margin-bottom: 20px;
}

#app > div > div > div.menu-content > div.content > div > div > div:nth-child(1) > input[type=button] {
    background-color: green;
    color: white;
    padding: 10px;
    border: 0px;
    margin-top: 20px;
    margin-bottom: 20px;
    border-radius: 5px;
    cursor: pointer;
}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > ul > ul > li > input[type=text] {
    width: 400px;
    height: 40px;
    border: 1px solid #243264;
}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > ul > ul > div > ul > li > input[type=text] {
    width: 400px;
    height: 40px;
    border: 1px solid #243264;
}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > ul > ul > div > ul > div > ul > li > input[type=text] {
    width: 400px;
    height: 40px;
    border: 1px solid #243264;
}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > ul > ul > div > ul > div > ul > div > ul > li > input[type=text] {
    width: 400px;
    height: 40px;
    border: 1px solid #243264;
}

#app > div > div > div.menu-content > div.content > div > div > div.list.Mission > ul > ul > div > ul > div > ul > div > ul > div > ul > li > input[type=text] {
    width: 400px;
    height: 40px;
    border: 1px solid #243264;
}



</style>