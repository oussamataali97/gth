<template>
  <div class="admin">
    <div class="header">
        <Nav />
      </div>


    <div class="container">


      <div class="menu-content">
        <div class="menu">
        </div>
        <div class="content">

                <h3>Gestion des Notes de Frais</h3>


                <div class="rechercher">
                    <input type="text" v-model="rechercher" placeholder="Recherche un note de frais de l'inspecteur">
                </div>

                <div class="list">
                    <table class="content-table" border="1">
                      <thead>
                            <tr>
                                <th>Période de déplacement </th>
                                <th>Date D'édition</th>
                                <th>Nom</th>
                                <th>Prénom</th>
                                <th>Justificatifs</th>
                                <th>Prix Total</th>
                                <th>Processus de Validation</th>
                                <th>Etat de Paiment</th>
                                <th>Date de Paiment</th>
                                <th>Actions</th>
                                <th>Notification</th>

                            </tr>
                      </thead>
                      <tbody>
                            <tr v-for="(index, i) in filterFrais" :key="index._id">
                                <td>{{ index.mois +'/'+ index.annee }}</td>
                                <td> {{ new Date(index.date).toLocaleDateString() }}</td>
                                <td>{{ index.nom }}</td>
                                <td>{{ index.prenom }}</td>
                                <td>
                                  <li @click="generateWord(index._id, index.nom, index.prenom)"><i class="fa-solid fa-file-word"></i></li>
                                  <li @click="generatexslx(index._id)"><i class="fa-solid fa-file-excel" ></i></li>
                                </td>
                                <td>{{ index.totalGeneral }} DH</td>
                                <td>
                                  <button type="submit" :class="index.valideRH ? 'succesButton' : 'echecButton'" @click="validerh(index._id)">Validation par DT - RH </button>
                                  <button type="submit" v-if="index.valideRH" :class="index.valideDIR ? 'succesButton' : 'echecButton'" @click="validedir(index._id, i)">Validation Administration</button></td>
                                <td>
                                  <select  v-model="frais[i].typePaiment"  v-if="index.valideDIR == true" :disabled="index.etatPaiment">
                                     <option v-for="name in categoryPaiment" :key="name" :value="name" > {{ name }} </option>
                                  </select>
                                  <input type="text" v-model="frais[i].refTransaction" v-if="index.valideDIR == true" :disabled="index.etatPaiment">
                                  <input v-if="index.valideDIR == true && index.etatPaiment == false" type="button" value="confirmer" @click="paiment(index._id, i)">
                                  <input v-if="index.etatPaiment == true" type="button" value="supprimer" @click="supprimerPaiment(index._id)">
                                </td>
                                <td>
                                  <input type="date" v-model="frais[i].datePaiment" v-if="index.valideDIR == true && index.validerDatePaiment == false" :disabled="index.validerDatePaiment">
                                  <span v-if="index.validerDatePaiment == true"> {{ new Date(frais[i].datePaiment).toLocaleDateString() }}</span>
                                  <input v-if="index.valideDIR == true && index.validerDatePaiment == false" type="button" value="confirmer" @click="paimentDate(index._id, i)">
                                </td>
                                <td>
                                  <button type="submit" @click="supprimerFrais(index._id, index.mois, index.annee, index.matricule, i)">supprimer</button>
                                </td>
                                <td>
                                  <li v-if="index.notification" @click="DeleteCheckNotificationFrais(index._id, i)"><i class="fa-solid fa-eye"></i></li>
                                </td>
                            </tr>
                      </tbody>
                    </table>
                </div>


          </div>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import Nav from "@/components/Admin/Nav.vue";
import Service from "../Service";
import io from 'socket.io-client'
const socket = io("http://localhost:5000");



export default {
  name: "admin",
  data() {
    return {
      frais : [],
      rechercher: null,
      etatPaiment : [],
      refTransaction : [],
      datePaiment : [],
      categoryPaiment : ["espèce", "virement", "chèque", "envoi"],
      nom  : null,
      prenom : null,
      matricule : null
    };
  },
  components: {
    Nav
  },


  computed : {
      filterFrais() {
            return this.frais.filter((item) => {
              if(!this.rechercher)
              {
                return item;
              }
                let date = item.datePaiment.toLocaleDateString();
                return !item.nom.toLowerCase().indexOf(this.rechercher.toLowerCase()) || !item.prenom.toLowerCase().indexOf(this.rechercher.toLowerCase()) || !date.indexOf(this.rechercher);
            })
      }
  },


  methods: {

    DeleteCheckNotificationFrais(id, i) {

      Service.deleteCheckNotificationFrais(id, this.matricule)
        .then((result) => {
            if(result) {
              this.frais[i].notification = false;
            }
        })
        .catch((error) => {
            console.log(error)
        });

    },

    formtDate(value) {
        return value.toLocaleDateString();
    },

    supprimerFrais(fraiId, mois, annee, matricule, i) {

        Service.SupprimerFrais(fraiId, mois, annee, matricule)
        .then((res) => {
          if(res) {
              this.frais.splice(i, 1);
          }
        })
        .catch((error) => {
          console.log(error.message);
        });
    },

    validerh(fraiId) {

        Service.validRH(fraiId)
        .then((res) => {
          if(res) {
               this.$router.go("/gestionfrais");
          }
        })
        .catch((error) => {
          console.log(error.message);
        })
    },

    validedir(fraiId, i) {

        Service.validDIR(fraiId)
        .then((res) => {
          if(res) {
              this.frais[i].valideDIR = true;
          }

        })
        .catch((error) => {
          console.log(error.message);
        })
    },

    paiment(fraiId, i) {

        Service.Paiment(fraiId, this.frais[i].typePaiment, this.frais[i].refTransaction)
        .then((res) => {
          if(res) {
               this.$router.go("/gestionfrais");
          }
        })
        .catch((error) => {
          console.log(error.message);
        });
    },

    supprimerPaiment(fraiId) {

        Service.SupprimerPaiment(fraiId)
        .then((res) => {
          if(res) {
               this.$router.go("/gestionfrais");
          }
        })
        .catch((error) => {
          console.log(error.message);
        });
    },

    paimentDate(fraiId, i) {
        Service.PaimentDate(fraiId, this.frais[i].datePaiment)
        .then((res) => {
          if(res) {
               this.$router.go("/gestionfrais");
          }
        })
        .catch((error) => {
          console.log(error.message);
        });
    },

    downloadFileExcel(response) {
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
        link.download = "resume.xlsx";
        link.click();
        setTimeout(function() {
            window.URL.revokeObjectURL(data);
        }, 100);
    },

    downloadFileWord(response) {
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

    generatexslx(fraiId) {

      Service.GenerateXSLX(fraiId)
      .then((response) => {
          this.downloadFileExcel(response);
      })
      .catch((error) => {
        console.log(error.message)
      })

    },

    generateWord(fraiId, nom, prenom) {

      Service.generateWORD(fraiId, nom, prenom)
      .then((response) => {
          this.downloadFileWord(response);
      })
      .catch((error) => {
        console.log(error.message)
      })

    },
  },


created() {

      if(!sessionStorage.getItem("token"))
      {
        this.$router.push("/")
      } else {

          // info account current
          this.nom = sessionStorage.getItem("nom");
          this.prenom = sessionStorage.getItem("prenom");
          this.matricule = sessionStorage.getItem("id");

          Service.Read()
          .then((response) => {
              this.frais = response.data.result;
          })
          .catch((error) => {
              console.log(error.message);
          });

          // Stream for get Online users
          Service.Online(this.matricule)
          .then((result) => {
            if(result) {
                // Socket admins
                socket.on("admins", (msg) => {
                  this.admins = msg;
                });
            }
          })
          .catch((error) => {
            console.log(error);
          });
      }

},

destory() {
          // reject online
      Service.rejectOnline(sessionStorage.getItem("id"))
      .then((result) => {
            console.log(result.data.response);
      })
      .catch((error) => {
            console.log(error);
      });
}



};
</script>

<style scoped>
.admin {
  width: 100%;

}

.admin .container {
  display: flex;
  justify-content: flex-start;
  align-items: flex-start;
  flex-direction: column;
  width: 100%;
  height: 100%;
}
.admin .container .header {
  width: 100%;
  height: 80px;
  padding: 0px;
  margin: 0px;
  box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 24px;
}
.admin .container .menu-content {
  padding: 0px;
  margin: 0px;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
}

.admin .container .menu-content .content {
  width: 100%;
  height: 100%;
}

.admin .container .menu-content .content .list{
  width: 100%;
  height: 100%;
}
.admin .container .menu-content .content .list .content-table {

  border-collapse: collapse;
  margin: 25px 0;
  font-size: 0.9em;
  width: 100%;

}
#app > div > div > div.menu-content > div.content > div.rechercher {

    display: flex;

    justify-content: center;
    margin: 20px auto;

}

#app > div > div > div.menu-content > div.content > div.rechercher > input[type=text] {
    border: 1px solid #56c945;
    height: 40px;
    width:50%;
    background-color: #bdf3061a;
    font-size: 15px;
    outline: 0px;
    border-radius: 20px;
}


.admin .container .menu-content .content .list .content-table thead tr {

    background-color: red;
    color: white;
    font-weight: bold;
    text-align: left;

}
.admin .container .menu-content .content .list .content-table thead tr th {

    background-color: #56c945;
    color: white;
    padding: 5px;
}

#app > div > div > div.menu-content > div.content > div.list > table > tbody > tr > td:nth-child(1) {
  background-color: #f3e70a;
}


#app > div > div > div.menu-content > div.content > div.list > table > tbody > tr > td {
  border-bottom: 1px solid #dddddd;
  background-color: white;
}


#app > div > div > div.menu-content > div.content > div.list > table > tbody > tr:last-of-type {
  background-color: #243064;
}


#app > div > div > div.menu-content > div.content > div.list > table > tbody > tr > td:nth-child(5) {
  display: flex;
  flex-direction: row;
}

#app > div > div > div.menu-content > div.content > div.list > table > tbody > tr > td > li {
  list-style: none;
}

#app > div > div > div.menu-content > div.content > div.list > table > tbody > tr > td > li:nth-child(1) > svg {

    color: #1e6cfb;
    font-size: 30px;
    margin: auto;
    cursor: pointer;
}
#app > div > div > div.menu-content > div.content > div.list > table > tbody > tr > td > li:nth-child(2) > svg {

    color: green;
    font-size: 30px;
    margin: auto;
    cursor: pointer;
}

#app > div > div > div.menu-content > div.content > div.list > table > tbody > tr > td:nth-child(7) > input[type=text] {
    width: 80%;
    height: 30px;
    margin-right: 3px;
}

#app > div > div > div.menu-content > div.content > div.list > table > tbody > tr > td:nth-child(7) > input[type=button] {
    height: 30px;
    border: 1px solid #56c945;
    background-color: #bdf3061a;
    font-size: 15px;
    outline: 0px;
    color: #56c945;
    cursor: pointer;
    margin-left: 2px;
    margin-right: 2px;
}

#app > div > div > div.menu-content > div.content > div.list > table > tbody > tr > td:nth-child(7) > button {
  margin-left: 4px;
}


#app > div > div > div.menu-content > div.content > div.list > table > thead > tr > th:nth-child(7) {
  text-align: center;
}

#app > div > div > div.menu-content > div.content > div.list > table > tbody > tr > td:nth-child(8) > select {
  height: 30px;
  margin-right: 5px;
}

#app > div > div > div.menu-content > div.content > div.list > table > tbody > tr > td:nth-child(8) > input[type=text] {
  height: 30px;
  margin-right: 5px;

}

#app > div > div > div.menu-content > div.content > div.list > table > tbody > tr > td:nth-child(8) > input[type=button] {
  height: 30px;
  border: 1px solid #56c945;
  background-color: #bdf3061a;
  font-size: 15px;
  outline: 0px;
  color: #56c945;
  cursor: pointer;
}

#app > div > div > div.menu-content > div.content > div.list > table > tbody > tr > td:nth-child(9) > input[type=date] {
  height: 30px;
  margin-right: 5px;

}

#app > div > div > div.menu-content > div.content > div.list > table > tbody > tr > td:nth-child(9) > input[type=button] {
  height: 30px;
  border: 1px solid #56c945;
  background-color: #bdf3061a;
  font-size: 15px;
  outline: 0px;
  color: #56c945;
  cursor: pointer;
}


#app > div > div > div.menu-content > div.content > div.list > table > tbody > tr > td:nth-child(10) > button {
  height: 30px;
  border: 1px solid #c94545;
  background-color: #f306061a;
  font-size: 15px;
  outline: 0px;
  color: #c96a45;
  cursor: pointer;
}

.succesButton {

    background-color: #8ddfb78f;
    padding: 0.3rem 2rem;
    border: 0px;
    color: #0e6e01;
    border-radius: 5px;
    cursor: pointer;

}

.echecButton {

    background-color: #e76e6e8f;
    padding: 0.3rem 2rem;
    border: 0px;
    color: #8b0606;
    border-radius: 5px;
    cursor: pointer;
}


h3 {
  width: 100%;
  margin: 0;
  color: white;
  background: linear-gradient(346deg, rgba(207, 31, 33, 1) 0%, rgba(24, 86, 161, 1) 100%);
  text-align: center;
  text-align: center;
  padding: 10px;
  font-size: 25px;
}

</style>