<template>

  <div class="creationClient">

    <h3>Information d'offre</h3>

    <h3 class="titre-client">Client :</h3>


    <div class="formCreation">


      <div>
        <label for="adresse">Raison sociale / Nom</label>
        <input type="text" disabled v-model="client.raisonSocial">
      </div>

      <div>
        <label for="adresse">Adresse</label>
        <input type="text" disabled v-model="client.adresse">
      </div>

      <div>
        <label for="ville">Ville</label>
        <input type="text" disabled v-model="client.ville">
      </div>

      <div>
        <label for="codePostal">Code Postal</label>
        <input type="text" disabled v-model="client.codePostal">
      </div>

      <div>
        <label for="pays">Pays</label>
        <input type="text" disabled v-model="client.pays">
      </div>

      <div>
        <label for="nom">E-mail</label>
        <input type="text" disabled v-model="client.email">
      </div>

      <div>
        <label for="nom">ICE</label>
        <input type="text" disabled v-model="client.ice">
      </div>

      <div>
        <label for="nom">Référence client</label>
        <input type="text" disabled v-model="client.refClient">
      </div>
      <div>
        <label for="nom"></label>
        <input type="hidden" disabled v-model="client._id">
      </div>
    </div>

    <h3 class="titre-interlocuteur">Interlocuteur :</h3>


    <div class="formCreation">

        <div>
            <label for="nom">Nom</label>
            <input disabled type="text" v-model="interlocuteur.nom">
        </div>

        <div>
            <label for="adresse">Prénom </label>
            <input disabled type="text" v-model="interlocuteur.prenom">
        </div>

        <div>
            <label for="ville">E-mail</label>
            <input disabled type="text" v-model="interlocuteur.email">
        </div>

        <div>
            <label for="codePostal">Fonction</label>
            <input disabled type="text" v-model="interlocuteur.fonction">
        </div>

        <div>
            <label for="pays">Téléphone</label>
            <input disabled type="text" v-model="interlocuteur.telephone">
        </div>

        <div>
            <label for="_id"></label>
            <input disabled type="hidden" v-model="interlocuteur._id">
        </div>

        <div>
            <label for="clientId"></label>
            <input disabled type="hidden" v-model="interlocuteur.clientId">
        </div>

    </div>



    <h3  class="titre-interlocuteur">Référence de l'affaire : </h3>
    <div class="formCreation">

      <div>
        <label for="Numéro d'affaire">Numéro d'affaire</label>
        <input type="text" disabled v-model="numeroAffaire">
      </div>

      <div>
        <label for="Appareil et accessoir de levage" >Apporteur d'affaire</label>
        <input type="text" disabled v-model="apporteurAffaire">
      </div>

    </div>

    <h3  class="titre-interlocuteur">Numéro d'affaire & Apporteur d'affaire: </h3>

    <div class="formCreation">

      <div>
        <label for="Numéro d'affaire">Numéro d'affaire</label>
        <input type="text" disabled v-model="numeroAffaire">
      </div>

      <div>
        <label for="Appareil et accessoir de levage" >Apporteur d'affaire</label>
        <input type="text" disabled v-model="apporteurAffaire">
      </div>

    </div>

    <h3  class="titre-interlocuteur">Mission : </h3>
      <div class="formCreation" v-for="(mission) in missions" :key="mission._id">
            <div>
              <label for="Numéro d'affaire">Type de Mission</label>
              <input type="text" disabled :value="mission.typeMission">
            </div>

            <div>
              <label for="Appareil et accessoir de levage" >Code Mission</label>
              <input type="text" disabled :value="mission.codeMission">
            </div>

            <div>
              <label for="Appareil et accessoir de levage" >Prix</label>
              <input type="text" disabled :value="mission.prix">
            </div>

            <div>
              <label for="Appareil et accessoir de levage" >Devis</label>
              <input type="text" disabled :value="mission.devis">
            </div>

            <div class="equipement">
                  <div v-for="eq in mission.equipement" :key="eq">
                    <label for="Appareil et accessoir de levage" >equipement</label>
                    <input type="text" disabled :value="eq">
                  </div>

                  <div v-for="q in mission.qte" :key="q">
                    <label for="Appareil et accessoir de levage" >Quantite</label>
                    <input type="text" disabled :value="q">
                  </div>
            </div>
      </div>


  </div>
</template>

<script>
import Service from '../../../../Service';

export default {
  data() {
    return {
        idAffaire : null,
        apporteurAffaire : null,
        numeroAffaire : null,

        client : {
            raisonSocial : null,
            adresse: null,
            ville: null,
            codePostal: null,
            pays: null,
            email: null,
            ice: null,
            refClient: null,
            _id: null
        },

        interlocuteur : {
            nom : null,
            prenom : null,
            email : null,
            fonction : null,
            telephone : null,
            _id : null,
            clientId :null
        },

        missions: []
    }
  },
    components: {
  },

  props : {
    infoAffaire : Array,
  },

  created()
  {
        this.idAffaire = this.infoAffaire[0]._id;
        this.apporteurAffaire = this.infoAffaire[0].apporteurAffaire;
        this.numeroAffaire = this.infoAffaire[0].numeroAffaire;

        Service.selectClient(this.infoAffaire[0].clientId)
        .then((result) => {
            this.client = result.data.client
        })
        .catch((err) => {
            console.log(err.message)
        });

        Service.selectInterlocuteurs(this.infoAffaire[0].interlocuteurId)
        .then((result) => {
          this.interlocuteur = result.data.interlocuteurs;
        })
        .catch((err) => {
            console.log(err.message)
        });

        Service.getMission(this.numeroAffaire)
        .then((res) => {
          res.data.missions.forEach(element => {
              this.missions.push(element);
          });
        })
        .catch((err) => {
          console.log(err);
        })
  }

};
</script>

<style scoped>


.creationClient {
  width: 100%;
  height: 100%;
  margin: 0px;
  padding: 10px;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: flex-start;
}

.creationClient h3:nth-child(1) {
  width: 100%;
  height: fit-content;
  padding: 5px;
  color: white;
  background-color: #243064;
  text-align: center;

}
.creationClient .titre-client {
    width: fit-content;
    height: fit-content;
    padding: 5px;
    color: white;
    background-color: #243064;

}
.creationClient .titre-interlocuteur {
    width: fit-content;
    height: fit-content;
    padding: 5px;
    color: white;
    background-color: #243064;

}
.succes {
  background-color: #69cd5b;
  color: white;
  padding: 10px;
  width: 100%;
  height: fit-content;
}

.echec {
  background-color: RED;
  color: white;
  padding: 10px;
  width: 100%;
  height: fit-content;
}

.formCreation {

  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
  flex-wrap: wrap;

}

.formCreation div {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  width: 45%;
}
.formCreation div label {
  margin-left:10px;
  margin-bottom: 5px;
  font-size: 14px;
  color :#243064;
}
.formCreation div input {
  height: 40px;
  margin-left:10px;
  margin-bottom: 5px;
  border: 1px solid #243064;
  padding:5px;
}.formCreation div input:focus-within {
  outline: 1px solid #cf1f21 ;
  border:0;

}

.formCreation div select {
  height: 40px;
  margin-left:10px;
}

hr {
  width: 100%;
  background-color: black;
  height: 10px;
}
.enregitreAffaire {
  background-color: green;
  color: white;
  cursor: pointer;
}
.file {
  background-color: white;
  color: black;
  cursor: pointer;
}
.file button{
  background-color: white;
  color: black;
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


#app > div > div > div.menu-content > div.content > div > div:nth-child(9) > div > input.enregitreAffaire {
  font-weight: bold;
  border: 0;
  border-radius: 10px;
  font-size: 18px;
}

#app > div > div > div.menu-content > div.content > div > div > div > div.equipement {
  width: 100%;
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-template-rows: 1fr;
  grid-column-gap: 0px;
  grid-row-gap: 0px;

}

#app > div > div > div.menu-content > div.content > div > div > div > div.equipement > div:nth-child(1) {
  width: 70%;
}

#app > div > div > div.menu-content > div.content > div > div > div > div.equipement > div:nth-child(1) input {
  width: 100%;
}

#app > div > div > div.menu-content > div.content > div > div > div > div.equipement > div:nth-child(2) {
  width: 20%;
}
#app > div > div > div.menu-content > div.content > div > div > div > div.equipement > div:nth-child(2) input {
  width: 100%;
}







</style>