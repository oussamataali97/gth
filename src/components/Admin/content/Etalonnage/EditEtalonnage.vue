<template>
  <div class="creationClient">

    <h3>CRÉER ÉTALONNAGE</h3>

    <Traitement :msg="msgEtalonnage" v-if="traitement == true"/>

    <p v-if="echec" :class="{ echec: echec }">
        {{ error }}
    </p>

    <div class="formCreation">
      <div>
        <label for="nom">Désignation</label>
        <input type="text" v-model="etalonnage.designation">
      </div>

      <div>
        <label for="adresse">Marque</label>
        <input type="text" v-model="etalonnage.marque">
      </div>

      <div>
        <label for="ville">Identification</label>
        <input type="text" v-model="etalonnage.identification">
      </div>

      <div>
        <label for="codePostal">Étalonnie Le</label>
        <input type="date" v-model="etalonnage.etalonnieLe">
      </div>

      <div>
        <label for="codePostal">Prochaine Étalonnage</label>
        <input type="date" v-model="etalonnage.prochaineEtalonnage">
      </div>

      <div>
        <label for="pays">Numéro Certificats d'étalonnage</label>
        <input type="text" v-model="etalonnage.certificatsEtalonnage">
      </div>

      <div>
        <label for="pays">Télécharger Certificat</label>
        <input type="file" multiple="multiple" placeholder="Télécharger Certificat" ref="file" @change="previewFile">
      </div>

      <div>
        <label for="pays">   </label>
        <input type="hidden">
      </div>

      <div>
        <input type="submit" value="Changé un étalonnage" @click="update()">
      </div>

      <div>
        <input type="submit" value="Quitter" @click="quitter()">
      </div>

    </div>

  </div>

</template>

<script>
import Service from "../../../../Service";

export default {
  data() {
    return {
      file : null,
      traitement : null,
      msgEtalonnage : null,
      etalonnage: {
              designation : null,
              marque : null,
              identification : null,
              etalonnieLe : null,
              prochaineEtalonnage : null,
              certificatsEtalonnage : null,
      },
      succes: false,
      echec: false,
      error : null
    };
  },
  props : {
    etalonnageId : String,
  },
  methods: {
    // preciew file
    previewFile() {
              this.file = this.$refs.file.files[0];
    },

    // Update Interlocuteurs
    update() {
      Service.updateEtalonnage(this.etalonnageId, this.etalonnage, this.file)
      .then(() => {

          this.succes = true;
          setTimeout(() => {
              this.succes = false;
              return this.$router.go(this.$router.currentRoute)
          }, 5000);

      })
      .catch((error) => {
          this.error = error.message;
          console.error(`HTTP error: ${error.name} => ${error.message}`);
          throw "fail request at: GET /refreshtime";
      })
    }
  },

  created() {

    // get clients (raison Social)
    Service.selectEtalonnage(this.etalonnageId)
      .then((result) => {
         this.etalonnage = result.data.etalonnage;
         var etalonnieLe = new Date(result.data.etalonnage.etalonnieLe).toISOString().slice(0, 10);
         this.etalonnage.etalonnieLe = etalonnieLe;

         var prochaineEtalonnage = new Date(result.data.etalonnage.prochaineEtalonnage).toISOString().slice(0, 10);
         this.etalonnage.prochaineEtalonnage = prochaineEtalonnage;
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
.creationClient {
  width: 100%;
  height: 100%;
  margin: 0px;
  padding: 10px;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
}

.creationClient h3 {
  width: 100%;
  height: fit-content;
  padding: 5px;
  color: white;
  background-color: #243064;
  text-align: center;

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

#app > div > div > div.menu-content > div.content > div > div > div > div:nth-child(9) > input[type=submit] {
    background-color: green;
    color: white;
    border: 0;
    margin-top: 30px;
    cursor: pointer;
}

#app > div > div > div.menu-content > div.content > div > div > div > div:nth-child(10) > input[type=submit] {
    background-color: red;
    color: white;
    border: 0;
    margin-top: 30px;
    cursor: pointer;
}


</style>