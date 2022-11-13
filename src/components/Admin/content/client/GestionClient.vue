<template>
    <div class="gestionInspecteur">

      <h3  v-if="flagEditClient == false && flagInfoClient == false">FICHES DES CLIENTS</h3>

      <div class="rechercher-table" v-if="flagEditClient == false && flagInfoClient == false">


            <div class="rechercher">
                <input type="text" v-model="rechercher" placeholder="Recherche un client">
            </div>

            <table id="inspecteurs">
              <tr>
                <th></th>
                <th>Raison sociale</th>
                <th>Référence client</th>
                <th>E-mail</th>
                <th>Téléphone </th>
                <th>Adresse</th>
                <th>Ville</th>
                <th>Pays</th>
                <th>ICE</th>
                <th>Actions</th>
              </tr>
              <tr v-for="(client, i) in filterClients" :key="client._id">
                <td>
                  <input type="checkbox" :value="client._id" v-model="checkedClients" style="width: 20px;">
                </td>
                <td>{{ client.raisonSocial }}</td>
                <td>{{ client.refClient }}</td>
                <td>{{ client.email }}</td>
                <td>{{ client.telephone }}</td>
                <td>{{ client.adresse }}</td>
                <td>{{ client.ville }}</td>
                <td>{{ client.pays }}</td>
                <td>{{ client.ice }}</td>
                <td>
                  <a @click="deleteClient(i)"><i class="fa-solid fa-trash"></i></a>
                  <a @click="editClient(i)"><i class="fa-solid fa-pen-to-square"></i></a>
                  <a @click="informationClient(i)"><i class="fa-solid fa-database"></i></a>
                </td>
              </tr>
            </table>

            <div class="deleteAll" v-show="checkedClients.length > 1">
              <input type="submit" value="Supprimer tout" @click="deleteClients()">
            </div>

      </div>



    <!--  Start Edit Client   -->
    <EditClient :infoClient="infoClient" v-if="flagEditClient == true" />
    <!--  End Edit Client   -->

    <!--  Start Info Client   -->
    <InfoClient :infoClient="infoClient" v-if="flagInfoClient == true" />
    <!--  End Info Client   -->

  </div>

</template>

<script>
import Service from "../../../../Service";
import EditClient from "./EditClient.vue";
import InfoClient from "./InfoClient.vue";
export default {
  name: "gestionInterlocuteur",
  components: {
    EditClient,
    InfoClient
  },
  data() {
    return {
      rechercher: null,
      clients : [],
      checkedClients : [],
      msg : null,
      flagEditClient : false,
      flagInfoClient : false,
      infoClient : [],
    };
  },

  computed : {
            filterClients() {
            return this.clients.filter((item) => {
              if(!this.rechercher)
              {
                return item
              }
                return !item.refClient.toLowerCase().indexOf(this.rechercher.toLowerCase()) ||
                       !item.raisonSocial.toLowerCase().indexOf(this.rechercher.toLowerCase()) ||
                       !item.telephone.toString().indexOf(this.rechercher.toString()) ||
                       !item.email.toLowerCase().toString().indexOf(this.rechercher.toString().toLowerCase()) ||
                       !item.ice.indexOf(this.rechercher) ||
                       !item.ville.toLowerCase().toString().indexOf(this.rechercher.toLowerCase()) ||
                       !item.adresse.toLowerCase().toString().indexOf(this.rechercher.toLowerCase()) ||
                       !item.codePostal.toLowerCase().toString().indexOf(this.rechercher.toString().toLowerCase());
            })
      }
  },


  methods: {

// delete more one client (clients)
    deleteClients() {


      // // Delete in Fron-end
      for(let i = 0; i < this.checkedClients.length; i++ ) {
        let indexClient = this.clients.indexOf(this.checkedClients[i]);
        //delete in front end
         this.clients.splice(indexClient, 1);
      }
        // delete Back end
        Service.deleteClient(this.checkedClients)
            .then((result) => {
              this.msg = result.data.msg;
        })
        .catch((error) => {
                this.msg = error.message;
                console.error(`HTTP error: ${error.name} => ${error.message}`);
                throw "fail request at: GET /refreshtime";
        });

    },
    // delete one client
    deleteClient(i) {
      const idClients = this.clients[i]._id;
      this.clients.splice(i, 1);
      Service.deleteClient(idClients)
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

  created() {
      Service.readClient()
      .then((result) => {
        this.clients = result.data.clients;
      })
      .catch((error) => {
          this.msg = error.message;
          console.error(`HTTP error: ${error.name} => ${error.message}`);
          throw "fail request at: GET /refreshtime";
      })
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

</style>