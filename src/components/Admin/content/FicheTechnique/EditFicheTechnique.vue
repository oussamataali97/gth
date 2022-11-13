<template>
  <div class="creationClient">

 <h3>MODIFIER FICHE TECHNIQUE MATÉRIEL</h3>

    <Traitement :msg="msgFicheTechnique" v-if="traitement == true"/>

    <p v-if="echec" :class="{ echec: echec }">
        {{ error }}
    </p>

    <div class="formCreation">
      <div>
        <label for="nom">Désignation</label>
        <input type="text" v-model="ficheTechnique.designation">
      </div>

      <div>
        <label for="adresse">Marque</label>
        <input type="text" v-model="ficheTechnique.marque">
      </div>

      <div>
        <label for="ville">Identification</label>
        <input type="text" v-model="ficheTechnique.identification">
      </div>

      <div>
        <label for="ville">Numéro Fiche Technique</label>
        <input type="text" v-model="ficheTechnique.numFiche">
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
        <input type="submit" value="Changé un fiche Technique" @click="update()">
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
      msgFicheTechnique : null,
      ficheTechnique: {
              designation : null,
              marque : null,
              identification : null,
              numFiche : null
      },
      succes: false,
      echec: false,
      error : null
    };
  },
  props : {
    fichetechniqueId : String,
  },
  methods: {
    // preciew file
    previewFile() {
              this.file = this.$refs.file.files[0];
    },

    // Update Interlocuteurs
    update() {
      Service.updateFicheTechnique(this.fichetechniqueId, this.ficheTechnique, this.file)
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
    Service.selectFicheTechnique(this.fichetechniqueId)
      .then((result) => {
         this.ficheTechnique = result.data.ficheTechnique;
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

#app > div > div > div.menu-content > div.content > div > div > div > div:nth-child(7) > input[type=submit] {
    background-color: green;
    color: white;
    border: 0;
    margin-top: 30px;
    cursor: pointer;
}

#app > div > div > div.menu-content > div.content > div > div > div > div:nth-child(8) > input[type=submit] {
    background-color: red;
    color: white;
    border: 0;
    margin-top: 30px;
    cursor: pointer;
}


</style>