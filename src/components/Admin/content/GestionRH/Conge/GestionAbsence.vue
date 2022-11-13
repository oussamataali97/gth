<template>

    <div class="gestionInspecteur">

      <h3>LISTE DEMANDE ABSENCE</h3>

      <p v-if="succes" :class="{ succes: succes }">
        {{ msg }}
      </p>

      <p v-if="echec" :class="{ echec: echec }">
        {{ msg }}
      </p>

      <div class="rechercher-table">

            <div class="rechercher">
                <input type="text" v-model="rechercher" placeholder="Recherche un Absence">
            </div>

            <table id="inspecteurs">
              <tr>
                <th> </th>
                <th>Date Congé</th>
                <th>Nom</th>
                <th>Prénom</th>
                <th>Type d'absence</th>
                <th>Motif</th>
                <th>pj</th>
                <th>Du</th>
                <th>Au</th>
                <th>Nomber de Jours</th>
                <th>reset</th>
                <th>status</th>
                <th>Actions</th>

              </tr>
              <tr v-for="(item, i) in filterAbsence" :key="item._id">
                <td><input type="checkbox" :value="item.filename" v-model="checkedAbsences" style="width: 20px;"></td>
                <td>{{ new Date(item.date).toLocaleDateString() }}</td>
                <td>{{ item.nom }}</td>
                <td>{{ item.prenom }}</td>
                <td>{{ item.typeAbsence }}</td>
                <td>{{ item.motif }}</td>
                <td>
                    <a @click="certficatAbsence(item.key)"><i class="fa-solid fa-certificate"></i></a>
                </td>
                <td>{{ item.dateDebutConge }}</td>
                <td>{{ item.dateFinConge }}</td>
                <td>{{ item.nomberJours }}</td>
                <td>{{ item.nomberJoursOuvrables }}</td>
                <td v-if="item.status == true">accordé</td>
                <td v-if="item.status == false">non accordé</td>
                <td>
                  <a v-show="item.status == false" @click="valideAbsence(item._id, item.adminId, i)"><i class="fa-solid fa-check"></i></a>
                  <a v-show="item.status == true" @click="invalideAbsence(item._id, item.adminId, i)"><i class="fa-solid fa-xmark"></i></a>
                  <a @click="deleteAbsence(i,item._id, item.adminId, item.key)"><i class="fa-solid fa-trash"></i></a>
                </td>
              </tr>
            </table>

            <div class="deleteAll" v-show="checkedAbsences.length > 1">
              <input type="submit" value="Supprimer tout" @click="deleteAbsences()">
            </div>

      </div> 




  </div>

</template>

<script>
import Service from "../../../../../Service";
export default {
  name: "gestionEtalonage",
  components: {
    
  },

  data() {
    return {
      succes: false,
      echec : false,
      rechercher : null,
      absence  : [],
      checkedAbsences : [],
      }
  },

  computed : {
          filterAbsence() {
            return this.absence.filter((item) => {
              if(!this.rechercher)
              {
                return item         
              } 
                return !item.nom.toLowerCase().toString().indexOf(this.rechercher.toLowerCase().toString()) ||
                !item.prenom.toLowerCase().toString().indexOf(this.rechercher.toLowerCase().toString()) ||
                !item.cin.toLowerCase().toString().indexOf(this.rechercher.toString()) ||
                !item.telephone.toString().indexOf(this.rechercher.toString());
          })
      }
  },
  methods: {

    // display Certificate Absence
    certficatAbsence(filename) {
          Service.displayAbsence(filename)
          .then((result) => {
              console.log(result);
          })
          .catch((error) => {
              console.log(error)
          });

    },

    // delete more one Salaries (Salaries)
    deleteAbsences() {

        //   // // Delete in Fron-end
        //   for(let i = 0; i < this.checkedSalaries.length; i++ ) {
        //     let indexSalaries = this.salaries.indexOf(this.checkedSalaries[i]);
        //     //delete in front end
        //     this.salaries.splice(indexSalaries, 1);
        //   }

        //   //    //  delete in db backend
        //   Service.deleteSalarie(this.checkedSalaries)
        //     .then((response) => { 
        //           console.log(response);    
        //     })
        //     .catch((error) => {
        //           this.msg = error.message;
        //           console.error(`HTTP error: ${error.name} => ${error.message}`);
        //           throw "fail request at: GET /refreshtime";
        //     });
   
    },

    // delete one Salarie
    deleteAbsence(i,id, adminId, filename) {
      this.absence.splice(i, 1);
      Service.deleteAbsence(id, adminId, filename)
      .then((result) => { 
            console.log(result)       
      })
      .catch((error) => {
            this.msg = error.message;
            console.error(`HTTP error: ${error.name} => ${error.message}`);
            throw "fail request at: GET /refreshtime";
      });
    },

    // valide Absence
    valideAbsence(id, adminId, i) {
      Service.valideAbsence(id, adminId)
      .then(() => { 
            this.absence[i].status = true;       
      })
      .catch((error) => {
            this.msg = error.message;
            console.error(`HTTP error: ${error.name} => ${error.message}`);
            throw "fail request at: GET /refreshtime";
      });
    },

    // invalide Absence
    invalideAbsence(id, adminId, i) {
      Service.invalideAbsence(id, adminId)
      .then(() => { 
            this.absence[i].status = false;       
      })
      .catch((error) => {
            this.msg = error.message;
            console.error(`HTTP error: ${error.name} => ${error.message}`);
            throw "fail request at: GET /refreshtime";
      });
    },



  },

  created() {
      Service.readConges()
      .then((result) => { 
        if(result.data.data != null) {
            result.data.data.forEach(element => {
                    this.absence.push(element);
            });
        }
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
  width: fit-content;
  padding: 10px;
  height: 40px;
  background-color: red;
  color: white;
  border: 0;
  cursor: pointer;
}


#inspecteurs > tr > td:nth-child(10) > a:nth-child(1) > svg  {
  color: red;
  font-size: 20px;
}

#inspecteurs > tr > td:nth-child(10) > a:nth-child(2) > svg  {
  color: blue;
  font-size: 20px;
}

#inspecteurs > tr > td:nth-child(10) > a:nth-child(3) > svg  {
  color: green;
  font-size: 20px;
}

#app > div > div > div.menu-content > div.content > div > h3 {
    background-color: #ff0000d4;
    padding: 15px;
}

#app > div > div > div.menu-content > div.content {
  background-color: white;
}

#inspecteurs > tr > td {
    background-color: white;
    color: #243064;
    border-bottom: 1px solid #243064;
}

#app > div > div > div.menu-content > div.content > div {
  background-color: white;
}


#inspecteurs > tr > td:nth-child(8) > a:nth-child(1) > svg {
  color: red;
}

#inspecteurs > tr > td:nth-child(8) > a:nth-child(3) > svg {
  color: green;
}

#inspecteurs > tr > td:nth-child(8) > a:nth-child(4) > svg {
  color: red;
}

#inspecteurs > tr > td:nth-child(8) > a:nth-child(5) > svg {
  color: orange;
}

</style>