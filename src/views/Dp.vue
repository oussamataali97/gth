<template>
  <div class="Intranet">


    <header style=" display: flex; justify-content:space-between; align-items:center; padding:0 20px; ">
    <div class="logo" @click="accueille()">
            <img src="./../assets/logo.png" alt="" style="width: 150px; cursor: pointer;">
        </div>

        <div class="button " style="display:flex; align-items:center;">
            <ul style="display: flex ;gap: 10px; align-items: center ;border-radius: 5px ;margin-right: 10px ; ;list-style: none; padding:10px">
               <div class="left" style="display: flex ;align-items:center; gap: 5px; padding:0px 10px;border-right: 2px solid rgb(202, 202, 202);">
                <div class="image">
                    <img src="./../assets/person.png" alt="" width="40px">
                </div>
                <div class="info" >
                    <li style="font-weight: 700;">Full Name : <span  style="font-weight: 500; color:gray;">{{ prenom }} {{ nom }} </span></li>
                    <li style="font-weight: 700;">Matricule : <span  style="font-weight: 500;color:gray;">{{ matricule }}</span></li>
                </div>
               </div>
                <li style="display: flex; align-items:center"><span style="font-weight: bold;"> <img src="./../assets/time.png" width="50px" alt=""> </span> {{ new Date().toLocaleDateString() }} | {{ new Date().getHours() + ":" + new Date().getMinutes() + ":" + new Date().getSeconds() }}</li>

            </ul>


            <button class="btnHome" @click="accueille()"> <i class="fa-solid fa-house"></i> Page D'acceuil</button>
            <button class="btnLogout" @click="deconnexion()"> <i class="fa-solid fa-right-from-bracket"></i> Deconexion</button>
        </div>


    </header>

     <div class="title" v-if="this.status == 'admin'">
        <p>  <i class="fa-solid fa-recycle"></i> DEPARTEMENT <span class="spn">PRODUCTION</span> </p>
    </div>


    <div class="parent">



          <div @click="gestionProduction()" v-if="status != 'inspecteur'">
            <div>
              <i class="fa-brands fa-product-hunt"></i>
              <p>Gestion Production</p>
            </div>
          </div>

          <div @click="gestionCalendrier()" v-if="status != 'inspecteur'">
            <div>
                  <i class="fa-solid fa-calendar-check"></i>
                  <p>Gestion Programmation et Planification</p>
            </div>
          </div>


          <div @click="outilRapports()" v-if="status != 'inspecteur'">
            <div>
                  <i class="fa-solid fa-chart-line"></i>
                  <p>Outil - Rapports</p>
            </div>
          </div>

    </div>

    <div class="copyright">
      <p>© 2022 GTHCONSULT BUREAU DE CONTRÔLE AGRÉÉ PAR L'ETAT</p>
    </div>



  </div>
</template>

<script>
import Service from '../Service';
import io from 'socket.io-client'
const socket = io("http://localhost:5000");
// @ is an alias to /src

export default {

  name: "Intranet",
  data() {
    return {
      status: null,
      admins : [],
      matricule : null
    }
  },
  components: {

  },

  methods : {

    gestionCalendrier() {
      return this.$router.push("/gestioncalendrier");
    },

    outilRapports() {
    },


    gestionProduction() {
      return this.$router.push("/production");
    },

    deconnexion() {

      // reject online
      Service.rejectOnline(sessionStorage.getItem("id"))
      .then((result) => {
            console.log(result.data.response);
      })
      .catch((error) => {
            console.log(error);
      });


      sessionStorage.removeItem("token");
      sessionStorage.removeItem("nom");
      sessionStorage.removeItem("prenom");
      sessionStorage.removeItem("status");
      sessionStorage.removeItem("email");
      sessionStorage.removeItem("id");
      return this.$router.push("/");
    },
    accueille() {
      return this.$router.push("/interface");
    },

  },

  computed : {
      filterOnline() {
            return this.admins.filter((item) => {
              if(item.connected == true)
              {
                return item;
              }
            })
      }
  },

  created() {

    if(!sessionStorage.getItem("token")){
        this.$router.push("/");
    } else {

          this.nom = sessionStorage.getItem("nom");
          this.prenom = sessionStorage.getItem("prenom");
          this.matricule = sessionStorage.getItem("id");
          this.status = sessionStorage.getItem("status");

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

          // check is exist notification Frais
          Service.checkNotificationFrais(this.matricule)
          .then((result) => {
            if(result) {
              // counter Notfication
              console.log(result.data.response);
            }
          })
          .catch((error) => {
            console.log(error);
          });

          // check is exist notification Frais
          Service.checkNotificationCalendrier(this.matricule)
          .then((result) => {
            if(result) {
              // counter Notfication
              console.log(result.data.response);
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



/* header style */

header{
    box-shadow: 0px 0px 5px rgb(173, 173, 173);
    background-color: white;
    widows: 100%;
}

.btnHome{
    padding:10px 20px;
    background-color: white;
    border:0;
    border-radius: 20px;
    color: rgb(228, 44, 44);
    font-weight: bold;
    outline:2px solid #243064;
    cursor: pointer;
    margin: 5px;
    transition: .3s ease-in;
}
.btnLogout{
    padding:10px 20px;
    background-color: rgb(228, 44, 44);
    border:0;
    color:white;
    font-weight: bold;
    border-radius: 20px;
    cursor: pointer;
    margin: 5px;
    transition: .3s ease-in;
}
.btnLogout:hover{
    background-color:#243064;
}

.btnHome:hover{
background-color: #cf1f21;
color:white;
outline:0;
}

.stats {
    font-size: 30px;
}

.stats i {
    margin-right: 10px;
    cursor: pointer;
    color:#243064;
    position: relative;
}

.stats i:hover {
color:#cf1f21;
}

.stats i::after {
  content:'1';
  font-size: 10px;
  position: absolute;
  color:white;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 10px;
  height: 10px;
  top:-10px;
  right: -10px;
  background-color: red;
  padding: 5px;
  border-radius: 100%;

}

/* end Header style  */

.Intranet .titre  {
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
}

.parent {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap:10px;
  background-color:white;
  justify-content: center;
  align-items: center;
  width: 100%;
  padding:0 80px;

}


/* start div 1 */
.parent div{
  display: flex;
  justify-content: start;
  align-items: center;
  min-height: 170px;
  flex-direction: column;
  font-size: 20px;
  width: 100%;
  font-weight: 700;
  cursor: pointer;
  transition: all .4s ease-in-out;
color:white;
}



/* start div 1 */
.parent div:nth-child(1) {
  width: 100%;
  height: 100%;
  overflow: hidden;
  cursor: pointer;
}

.parent div:nth-child(1) div {
  height: 100%;
  width: 100%;
  background-image: url("../assets/prodctionAvancee.jpg");
  background-size: cover;
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

.parent div:nth-child(1) div::before {
  content: "";
  height: 100%;
  width: 100%;
  background-color: #24306466;
  position: absolute;
  left: 0;
  top: 0;
}

.parent div:nth-child(1):hover div {
  transform: scaleY(1.1);
  transform: scaleX(1.1);
  transition: 0.5s;
}


.parent div:nth-child(1) div svg {
  color: white;
  font-size: 50px;
  z-index: 10;
}

.parent div:nth-child(1) div p {
    text-align: center;
    color: white;
    font-size: 18px;
    z-index: 10;
    /* font-weight: bold; */
    font-weight: bold;
}

/* End div 1 */


/* start div 2 */
.parent div:nth-child(2) {
  width: 100%;
  height: 100%;
  overflow: hidden;
  cursor: pointer;
}

.parent div:nth-child(2) div {
  height: 100%;
  width: 100%;
  background-image: url("../assets/gestioncalendar.jpg");
  background-size: cover;
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

.parent div:nth-child(2) div::before {
  content: "";
  height: 100%;
  width: 100%;
  background-color: #24642866;
  position: absolute;
  left: 0;
  top: 0;
}

.parent div:nth-child(2):hover div {
  transform: scaleY(1.1);
  transform: scaleX(1.1);
  transition: 0.5s;
}


.parent div:nth-child(2) div svg {
  color: white;
  font-size: 50px;
  z-index: 10;
}

.parent div:nth-child(2) div p {
    text-align: center;
    color: white;
    font-size: 18px;
    z-index: 10;
    /* font-weight: bold; */
    font-weight: bold;
}

/* End div 2 */


/* start div 3 */
.parent div:nth-child(3) {
  width: 100%;
  height: 100%;
  overflow: hidden;
  cursor: pointer;
}

.parent div:nth-child(3) div {
  height: 100%;
  width: 100%;
  background-image: url("../assets/etalonnage.jpg");
  background-size: cover;
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

.parent div:nth-child(3) div::before {
  content: "";
  height: 100%;
  width: 100%;
  background-color: #24306466;
  position: absolute;
  left: 0;
  top: 0;
}

.parent div:nth-child(3):hover div {
  transform: scaleY(1.1);
  transform: scaleX(1.1);
  transition: 0.5s;
}


.parent div:nth-child(3) div svg {
  color: white;
  font-size: 50px;
  z-index: 10;
}

.parent div:nth-child(3) div p {
    text-align: center;
    color: white;
    font-size: 18px;
    z-index: 10;
    /* font-weight: bold; */
    font-weight: bold;
}

/* End div 3 */

#app > div > div.copyright > p {
    font-weight: bolder;
    color: red;
}

#app > div > div.logo > ul {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}

#app > div > div.logo > div > li {
  color: green;
  text-decoration: none;
  list-style: none;
}

.title p{
    text-align: center;
    color:#cf1f21;
    font-size: 30px;
    font-weight: 700;
    margin:10px;

}



.spn{
   color:#243064;
   animation: typing 1s steps(10) infinite;
   overflow: hidden;
}

.fa-recycle{
  font-size: 39px;
}

@keyframes typing
{

   0%,90%,100%{
       opacity: 0;
   }
   30%,60%{
       opacity: 1;
   }
}


</style>
