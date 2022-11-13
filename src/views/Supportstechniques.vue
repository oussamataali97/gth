<template>
  <div class="admin">
    <div class="container">
      <div class="header">
        <Nav />
      </div>
      <div class="menu-content">
        <div class="menu">
        </div>
        <div class="content">

                <h3>Supports Techniques</h3>

                <div class="info">


                  <table>
                    <thead>
                      <tr>
                        <td>Nom :</td>
                        <td> {{ nom }}</td>

                      </tr>
                    </thead> 
                    <tbody>
                        <tr>
                          <td>Prénom: </td>
                          <td> {{ prenom }}</td>
                        </tr>
                        <tr>
                          <td>Date: </td>
                          <td> {{ new Date().toLocaleDateString() }}</td>
                        </tr>
                        <tr>
                          <td>Matricule: </td>
                          <td> {{ matricule }}</td>
                        </tr>
                    </tbody>
                  </table>

                    <div class="front">
                        <h2>Bibliothèque de documents</h2>

                        <p> Bienvenue dans votre bibliothèque de documents en un clic accédez à l' ensemble de vos documents , via le mouteur de recherche ou listes de dossiers partagés</p>

                        <p> Très important! : Propriété de GTHCONSULT toute divulgation externe, sauf autorisation, est interdite</p>

                        <div>
                          <input type="text" v-model="search" placeholder="Ici vous pouvez rechercher le fichier">
                        </div>

                        <ul class="searchList" v-for="index in filterFile" :key="index._id">
                            <li @click="handelPdf(`${index.name}`)"> {{ index.name }}</li>
                        </ul>
                    </div>

                </div>




                <ul class="list">
                  <li @click="elec = !elec"><i class="fa-solid fa-bolt"></i><p>ELECTRECITE</p></li>
                  <ul v-if="elec">
                      <li @click="flagZero = !flagZero">chap 00- sommaire</li>
                        <ul v-if="flagZero">
                          <li @click="handelPdf(`chap OO - sommaire`)">chap OO - sommaire</li>
                        </ul>


                      <li @click="flagOne = !flagOne">chap 01- intégrations</li>
                        <ul v-if="flagOne">
                          <li @click="handelPdf(`chap 01- intégrations`)">chap 01- intégrations</li>
                        </ul>


                      <li @click="flagTow = !flagTow">chap 02- présentation réglementation</li>
                        <ul v-if="flagTow">
                          <li @click="handelPdf(`Arrêté 28 juin 1938`)">Arrêté 28 juin 1938</li>
                          <li @click="handelPdf(`Buletin OFF Arrête MAROCAIN - ELEC`)">Buletin OFF Arrête MAROCAIN - ELEC</li>
                          <li @click="handelPdf(`chap 02-Presentation réglementation FR`)">chap 02-Presentation réglementation FR</li>
                        </ul>


                      <li @click="flagTree = !flagTree">chap 03- textes réglementaires FR</li>
                        <ul v-if="flagTree">
                          <li @click="flagTreeOne = !flagTreeOne">Normes électricité BT- HT -- Français</li>
                           <ul v-if="flagTreeOne">
                              <li @click="handelPdf(`>NFC 13 100`)">NFC 13 100</li>
                              <li @click="handelPdf(`NFC13-200F1Mai2011`)">NFC 13-200F1 Mai 2011</li>
                              <li @click="handelPdf(`NFC15-100_11-2008`)">NFC 15-100_11-2008</li>
                           </ul>
                          <li @click="handelPdf(`01_Decret_2010-1016_du_30_aout_2010_version_initiale_FP`)">01_Decret_2010-1016_du_30_aout_2010_version_initiale_FP</li>
                          <li @click="handelPdf(`02_Decret_2010-1017_du_30_aout_2010_version_initiale_FP`)">02_Decret_2010-1017_du_30_aout_2010_version_initiale_FP</li>
                          <li @click="handelPdf(`03_Decret_2010-1018_du_30_aout_2010_version_initiale_FP`)">03_Decret_2010-1018_du_30_aout_2010_version_initiale_FP</li>
                          <li @click="handelPdf(`04_Decret_2010-1118_Elec_Habilitation`)">04_Decret_2010-1118_Elec_Habilitation</li>
                          <li @click="handelPdf(`05_Arrete_2011-12-14_Ecl-Secu`)">05_Arrete_2011-12-14_Ecl-Secu</li>
                          <li @click="handelPdf(`06_Arrete_2011-12-15_Elec-Galvano`)">06_Arrete_2011-12-15_Elec-Galvano</li>
                          <li @click="handelPdf(`07_Arrete_2011-12-16_Elec-Labo`)">07_Arrete_2011-12-16_Elec-Labo</li>
                          <li @click="handelPdf(`08_Arrete_2011-12-19_Elec-Soudage`)">08_Arrete_2011-12-19_Elec-Soudage</li>
                          <li @click="handelPdf(`09_Arrete_2011-12-20_Elec-Amovible`)">09_Arrete_2011-12-20_Elec-Amovible</li>
                          <li @click="handelPdf(`10_Arrete_2011-12-21_Elec-Accred`)">10_Arrete_2011-12-21_Elec-Accred</li>
                          <li @click="handelPdf(`11_Arrete_2011-12-22_Elec-Competences`)">11_Arrete_2011-12-22_Elec-Competences</li>
                          <li @click="handelPdf(`12_Arrete_2011-12-23_Elec-Machines`)">12_Arrete_2011-12-23_Elec-Machines</li>
                          <li @click="handelPdf(`13_Arrete_2011-12-26_Verif-Elec`)">13_Arrete_2011-12-26_Verif-Elec</li>
                          <li @click="handelPdf(`>14_Arrete_2012-04-19 normes`)">14_Arrete_2012-04-19 normes</li>
                          <li @click="handelPdf(`15_Arrete_2012-04-20 dossier technique`)">15_Arrete_2012-04-20 dossier technique</li>
                          <li @click="handelPdf(`16_Arrete_2012-04-26 norme voisinage`)">16_Arrete_2012-04-26 norme voisinage</li>
                          <li @click="handelPdf(`17_Arrete_2012-04-30_Correctif-Elec`)">17_Arrete_2012-04-30_Correctif-Elec</li>
                          <li @click="handelPdf(`>18_circulaire DGT du 9 oct 2012`)">18_circulaire DGT du 9 oct 2012</li>
                          <li @click="handelPdf(`19.UTE-15-103-mars-04`)">19.UTE-15-103-mars-04</li>
                          <li @click="handelPdf(`UTE C 15-106 (12-2003) Section des conducteurs de protection`)">UTE C 15-106 (12-2003) Section des conducteurs de protection</li>
                          <li @click="handelPdf(`UTE-15-105-juil-03`)">UTE-15-105-juil-03</li>
                        </ul>


                      
                      <li @click="flagFour = !flagFour">chap 04- DTE ELEC</li>
                        <ul v-if="flagFour">
                          <li @click="handelPdf(`DTE-2019-ELEC 01_COURT-CIRCUIT`)">DTE-2019-ELEC 01_COURT-CIRCUIT</li>
                          <li @click="handelPdf(`DTE-2019-ELEC 02_SURCHARGES`)">DTE-2019-ELEC 02_SURCHARGES</li>
                          <li @click="handelPdf(`DTE-2019-ELEC 03_CHUTE-TENSION`)">DTE-2019-ELEC 03_CHUTE-TENSION</li>
                          <li @click="handelPdf(`DTE-2019-ELEC 04_SURTENSIONS`)">DTE-2019-ELEC 04_SURTENSIONS</li>
                          <li @click="handelPdf(`DTE-2019-ELEC 05_CONTACTS-DIRECTS`)">DTE-2019-ELEC 05_CONTACTS-DIRECTS</li>
                          <li @click="handelPdf(`DTE-2019-ELEC 06_CONTACTS-INDIRECTS`)">DTE-2019-ELEC 06_CONTACTS-INDIRECTS</li>
                          <li @click="handelPdf(`DTE-2019-ELEC 07_ESSAIS-MESURAGES`)">DTE-2019-ELEC 07_ESSAIS-MESURAGES</li>
                          <li @click="handelPdf(`DTE-2019-ELEC PV_MISE A LA TERRE DES PANNEAUX PV`)">DTE-2019-ELEC PV_MISE A LA TERRE DES PANNEAUX PV</li>
                        </ul>


                      <li @click="flagFive = !flagFive">chap 05 - Réglementation marocaine</li>
                        <ul v-if="flagFive">
                          <li @click="flagFiveOne = !flagFiveOne">Normes NM IMANOR</li>
                            <ul v-if="flagFiveOne">
                              <li @click="handelPdf(`06.1.012`)">06.1.012</li>
                              <li @click="handelPdf(`06.1.100`)">06.1.100</li>
                              <li @click="handelPdf(`06.1.101`)">06.1.101</li>
                              <li @click="handelPdf(`06.1.102`)">06.1.102</li>
                              <li @click="handelPdf(`06.1.103`)">06.1.103</li>
                              <li @click="handelPdf(`06.1.104`)">06.1.104</li>
                              <li @click="handelPdf(`06.1.105`)">06.1.105</li>
                              <li @click="handelPdf(`06.1.106`)">06.1.106</li>
                            </ul>
                          <li @click="handelPdf(`Arrêté 28 juin 1938`)">Arrêté 28 juin 1938</li>
                          <li @click="handelPdf(`Buletin OFF Arrête MAROCAIN - ELEC`)">Buletin OFF Arrête MAROCAIN - ELEC</li>
                        </ul>

                      <li @click="flagSix = !flagSix">chap 06 - Norme C18510 HAB</li>
                          <ul v-if="flagSix">
                             <li @click="handelPdf(`GTH_Habilitation Electrique UTE C18-510`)">GTH_Habilitation Electrique UTE C18-510</li>
                          </ul>


                      <li @click="flagSeven = !flagSeven">chap 07 - ELECTROTECH - Conception ELEC</li>
                          <ul v-if="flagSeven">
                             <li @click="handelPdf(`GTH-Support dimmensionnent ELEC_Version 1`)">GTH-Support dimmensionnent ELEC_Version 1</li>
                          </ul>



                      <li @click="flagEight = !flagEight">chap 08 - PGS  Procédure pour réaliser une inspection sans danger</li>
                          <ul v-if="flagEight">
                             <li @click="handelPdf(`PGS ELEC - Procédure Général de sécurité - Version Janvier 2022`)">PGS ELEC - Procédure Général de sécurité - Version Janvier 2022</li>
                          </ul>
                  </ul>
                  <li  @click="levage = !levage"><i class="fa-solid fa-weight-hanging"></i><p>LEVAGE</p></li>
                  <li  @click="incendie = !incendie"><i class="fa-solid fa-fire-extinguisher"></i><p>INCENDIE</p></li>
                  <li  @click="machine = !machine"><i class="fa-solid fa-screwdriver-wrench"></i><p>MACHINE</p></li>
                </ul>

              

          </div>
      </div>
    </div>
    <!-- <Pdf v-if="flagPdf == true" :filepath="filename"></Pdf> -->
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
         nom: null,
         prenom: null,
         matricule: null,
         elec : false,
         incendie :false,
         levage: false,
         machine: false,
         flagZero : false,
         flagOne : false,
         flagTow : false,
         flagTree : false,
         flagTreeOne: false,
         flagFour : false,
         flagFive : false,
         flagFiveOne: false,
         flagSix : false,
         flagSeven : false,
         flagEight : false,
         search : null,
         files : [],
    };
  },

  components: {
    Nav,
    
  },


  computed : {
      filterFile() {
            return this.files.filter((item) => {
              if(!this.search)
              {
                return null;     
              } 
                return !item.name.toLowerCase().indexOf(this.search.toLowerCase());
            })
      }
  },


  methods: {

    // handel Pdf Supports Techniques
    handelPdf(filename) {
      this.$router.push({path: '/showpdf', query : { name: filename}});
    }

  },

created() {

      if(!sessionStorage.getItem("token"))
      {
        this.$router.push("/")
      } else {

          this.nom = sessionStorage.getItem("nom");
          this.prenom = sessionStorage.getItem("prenom");
          this.matricule = sessionStorage.getItem("id");

        const arr = [
            "chap OO - sommaire",
            "chap 01- intégrations",
            "Arrêté 28 juin 1938",
            "Buletin OFF Arrête MAROCAIN - ELEC",
            "chap 02-Presentation réglementation FR",
            "NFC 13 100",
            "NFC 13-200F1 Mai 2011",
            "NFC 15-100_11-2008",
            "01_Decret_2010-1016_du_30_aout_2010_version_initiale_FP",
            "02_Decret_2010-1017_du_30_aout_2010_version_initiale_FP",
            "03_Decret_2010-1018_du_30_aout_2010_version_initiale_FP",
            "04_Decret_2010-1118_Elec_Habilitation",
            "05_Arrete_2011-12-14_Ecl-Secu",
            "06_Arrete_2011-12-15_Elec-Galvano",
            "07_Arrete_2011-12-16_Elec-Labo",
            "08_Arrete_2011-12-19_Elec-Soudage",
            "09_Arrete_2011-12-20_Elec-Amovible",
            "10_Arrete_2011-12-21_Elec-Accred",
            "11_Arrete_2011-12-22_Elec-Competences",
            "12_Arrete_2011-12-23_Elec-Machines",
            "13_Arrete_2011-12-26_Verif-Elec",
            "14_Arrete_2012-04-19 normes",
            "15_Arrete_2012-04-20 dossier technique",
            "16_Arrete_2012-04-26 norme voisinage",
            "17_Arrete_2012-04-30_Correctif-Elec",
            "18_circulaire DGT du 9 oct 2012",
            "19.UTE-15-103-mars-04",
            "UTE C 15-106 (12-2003) Section des conducteurs de protection",
            "UTE-15-105-juil-03",
            "DTE-2019-ELEC 01_COURT-CIRCUIT",
            "DTE-2019-ELEC 02_SURCHARGES",
            "DTE-2019-ELEC 03_CHUTE-TENSION",
            "DTE-2019-ELEC 04_SURTENSIONS",
            "DTE-2019-ELEC 05_CONTACTS-DIRECTS",
            "DTE-2019-ELEC 06_CONTACTS-INDIRECTS",
            "DTE-2019-ELEC 07_ESSAIS-MESURAGES",
            "DTE-2019-ELEC PV_MISE A LA TERRE DES PANNEAUX PV",
            "06.1.012",
            "06.1.100",
            "06.1.101",
            "06.1.102",
            "06.1.103",
            "06.1.104",
            "06.1.105",
            "06.1.106",
            "Arrêté 28 juin 1938",
            "Buletin OFF Arrête MAROCAIN - ELEC",
            "GTH_Habilitation Electrique  UTE C18-510",
            "GTH-Support dimmensionnent ELEC_Version 1",
            "PGS ELEC - Procédure Général de sécurité - Version  Janvier 2022"
            ];



          for(let i = 0; i < arr.length; i++) {
            this.files.push({
              id : i,
              name : arr[i]
            });
          }

          // Stream for get Online users
          Service.Online(sessionStorage.getItem("id"))
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
  display: flex;
  justify-content: center;
  align-items: center;
  height: 400vh;
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
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: flex-start;
}

.admin .container .menu-content .content .info {
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: flex-start;
  background-image: url("../assets/libary.jpg");
  background-size: cover;
  height: 800px;
}


.admin .container .menu-content .content .info .front {
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
}

.admin .container .menu-content .content .info .front h2 {
    text-align: center;
    font-size: 50px;
    margin: 0;
    margin-top: 20px;
    border-bottom: 5px solid #243064;
    border-top: 5px solid #243064;
    border-left: 5px solid #243064;
    width: fit-content;
    padding: 30px;
    background-color: white;
    color: #243064;
}

#app > div > div > div.menu-content > div.content > div > div > p:nth-child(2) {

    text-align: center;
    margin: 0;
    margin-top: 20px;
    padding: 30px;
    font-size: 18px;
    color: #243064;
    background-color: white;
    border-bottom: 5px solid #243064;
    border-top: 5px solid #243064;
    border-right: 5px solid #243064;

}


#app > div > div > div.menu-content > div.content > div > div > p:nth-child(3) {
    color: red;
    background-color: white;
    padding: 20px;
}

#app > div > div > div.menu-content > div.content > div > div > div {
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: center;
}

#app > div > div > div.menu-content > div.content > div > div > div input {
  width: 50%;
  height: 70px;
  border: 0;
  color: black;
  outline: 0;
  font-size: 18px;
}
#app > div > div > div.menu-content > div.content > div > div > ul {
    height: 100%;
    width: 50%;
    background-color: #ffffffb8;
    color: #243064;
    padding: 10px;
    cursor: pointer;
    transition: 0.3s;
    z-index: 10;
}



#app > div > div > div.menu-content > div.content > ul {
    width: 100%;
    background-color: #243064;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;

}

#app > div > div > div.menu-content > div.content > ul li {
    width: fit-content;
    display: flex;
    flex-direction: row;
    justify-content: flex-start;
    align-items: center;
    cursor: pointer;
}

#app > div > div > div.menu-content > div.content > ul li:hover svg {
  font-size: 35px;
  transition: 0.5s;
}

#app > div > div > div.menu-content > div.content > ul li svg {
  color: white;
  font-size: 30px;
  margin-left: 5px;
  margin-right: 5px;
}

#app > div > div > div.menu-content > div.content > ul li p {
  color: white;
  font-size: 30px;
}


#app > div > div > div.menu-content > div.content > ul > ul{

  margin-left: 200px;

}

#app > div > div > div.menu-content > div.content > ul > ul li {

    width: fit-content;
    cursor: pointer;
    font-size: 20px;
    color: #ddd;
    margin-bottom:10px;

}

#app > div > div > div.menu-content > div.content > ul > ul > ul {

  margin-left: 200px;

}


#app > div > div > div.menu-content > div.content > ul > ul > ul > li {

    width: fit-content;
    cursor: pointer;
    font-size: 18px;
    color: #ddd;
    margin-bottom:10px;

}









h3 {
    width: 100%;
    height: -webkit-fit-content;
    height: -moz-fit-content;
    height: fit-content;
    padding: 5px;
    margin: 0;
    color: white;
    background-color: #f1ac38;
    text-align: center;
    margin-bottom: 10px;
}

</style>