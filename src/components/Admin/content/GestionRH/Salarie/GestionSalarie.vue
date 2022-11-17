<template>

    <div class="gestionInspecteur">

      <h3 v-if="flagEditSalarie == false" >GESTION DES FICHES COLLABORATEURS</h3>

      <p v-if="succes && flagEditSalarie == false" :class="{ succes: succes }">
        {{ msg }}
      </p>

      <p v-if="echec && flagEditSalarie == false" :class="{ echec: echec }">
        {{ msg }}
      </p>

      <div class="rechercher-table" v-if="flagEditSalarie == false">

            <div class="rechercher">
                <input type="text" v-model="rechercher" placeholder="Recherche un Interlocuteur">
            </div>

            <table id="inspecteurs">
              <tr>
                <th> </th>
                <th>Nom</th>
                <th>prénom</th>
                <th>CIN/PASSPORT</th>
                <th>Telephone</th>
                <th>Email</th>
                <th>CIN</th>
                <th>Diplôme</th>
                <th>Photo</th>
                <th>Documents, A</th>
                <th>D, Médical</th>
                <th>Actions</th>
              </tr>
              <tr v-for="(item, i) in filterSalaries" :key="item._id">
                <td><input type="checkbox" :value="item._id" v-model="checkedSalaries" style="width: 20px;"></td>
                <td>{{ item.nom }}</td>
                <td>{{ item.prenom }}</td>
                <td>{{ item.identite }}</td>
                <td>{{ item.telephone }}</td>
                <td>{{ item.email }}</td>
                <td>
                  <a @click="certficatSalarie(item.cin)"><i class="fa-solid fa-certificate"></i></a>
                </td>
                <td>
                  <a @click="certficatSalarie(item.diplome)"><i class="fa-solid fa-certificate"></i></a>
                </td>
                <td>
                  <a @click="certficatSalarie(item.photo)"><i class="fa-solid fa-certificate"></i></a>
                </td>
                <td>
                  <a @click="certficatSalarie(item.autres)"><i class="fa-solid fa-certificate"></i></a>
                </td>
                <td>
                  <a @click="certficatSalarie(item.medical)"><i class="fa-solid fa-certificate"></i></a>
                </td>
                <td>
                  <a @click="deleteSalarie(item.cin, item.diplome, item.photo, item.autres, item.medical, i, item._id)"><i class="fa-solid fa-trash"></i></a>
                  <a @click="editSalarie(item._id)"><i class="fa-solid fa-pen-to-square"></i></a>
                </td>
              </tr>
            </table>

            <div class="deleteAll" v-show="checkedSalaries.length > 1">
              <input type="submit" value="Supprimer tout" @click="deleteSalaries()">
            </div>

      </div>



    <!--  Start Edit Inspecteur   -->
    <EditSalarie :salarieId="salarieId" v-if="flagEditSalarie == true" />
    <!--  End Edit Inspecteur   -->

  </div>

</template>

<script>
import Service from "../../../../../Service";
import EditSalarie from "./EditSalarie.vue";
export default {
  name: "gestionEtalonage",
  components: {
    EditSalarie,
  },
  data() {
    return {
      succes: false,
      echec: false,
      msg: null,
      rechercher: null,
      salaries: [],
      checkedSalaries : [],
      flagEditSalarie : false,
      salarieId : null
    };
  },

  computed : {
          filterSalaries() {
            return this.salaries.filter((item) => {
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

    // display Certificate Fiche Salarie
    certficatSalarie(filename) {
          Service.displaySalarie(filename)
          .then((result) => {
              console.log(result);
          })
          .catch((error) => {
              console.log(error)
          });

    },

    // delete more one Salaries (Salaries)
    deleteSalaries() {

          var salariesId = [];
          var cins = [];
          var diplomes = [];
          var photos = [];
          var autress = [];
          var medicals = []

          // Delete in Fron-end
          for(let i = 0; i < this.checkedSalaries.length; i++ ) {

            let indexSalaries = this.salaries.indexOf(this.checkedSalaries[i]);

            console.log(this.salaries[indexSalaries]._id)
            salariesId.push(this.salaries[indexSalaries]._id);
            cins.push(this.salaries[indexSalaries].cin);
            diplomes.push(this.salaries[indexSalaries].diplome);
            photos.push(this.salaries[indexSalaries].photo);
            autress.push(this.salaries[indexSalaries].autres);
            medicals.push(this.salaries[indexSalaries].medical);

          }

          // Delete in Fron-end
          for(let i = 0; i < this.checkedSalaries.length; i++ ) {
            let indexSalaries = this.salaries.indexOf(this.checkedSalaries[i]);
            //delete in front end "client"
            this.salaries.splice(indexSalaries, 1);
          }

          //  delete in db backend
          Service.deleteSalaries(cins, diplomes, photos, autress, medicals,  salariesId)
            .then((response) => {
                  console.log(response);
            })
            .catch((error) => {
                  this.msg = error.message;
                  console.error(`HTTP error: ${error.name} => ${error.message}`);
                  throw "fail request at: GET /refreshtime";
            });

    },

    // delete one Salarie
    deleteSalarie(cin, diplome, photo, autres, medical, i, salarieId) {
      this.salaries.splice(i, 1);
      Service.deleteSalarie(cin, diplome, photo, autres, medical, salarieId)
      .then((result) => {
            this.msg = result.data.msg;
      })
      .catch((error) => {
            this.msg = error.message;
            console.error(`HTTP error: ${error.name} => ${error.message}`);
            throw "fail request at: GET /refreshtime";
      });
    },

    // edit one Fiche Salarie
    editSalarie (salarieId) {
      this.flagEditSalarie = true;
      this.salarieId = salarieId;
    },

  },

  created() {
      Service.readSalaries()
      .then((result) => {
        this.salaries = result.data.salaries;
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