<template>
  <div class="indicateur">
      <div class="titre">
        <i class="fa-solid fa-magnifying-glass"></i>
        <h1><span>LES INDICATEURS</span> <span>DES AFFAIRES</span></h1>
      </div>
      <div class="text">
        <h2><i class="fa-solid fa-arrow-right"></i>Gestion administrative des l’organisation de gestions</h2>
        <span>
          Administrative des lorganise GTHCONSULT, concerne l’ensemble des tâches et des formalités
          administratives pour se confirme au respect des procédures de qualité, cette gestion administrative
          désigne l’ensemble des actions à mettre en œuvre pour permettre l’entreprise d’atteindre objectives
          stratégies.
        </span>
      </div>

      <div class="filter">
            <label for="nom">Rechercher par des années: </label>
            <select v-model="annee" @change="indicateur">
                <option v-for="item in years" :key="item" :value="item" > {{ item }} </option>
            </select>
      </div>

      <div class="redirect">
          <i class="fa-solid fa-circle-plus"></i>
      </div>

      <div class="statstique">
<!-- janvier, fevrier, mars, avril, mai, juin, juillet, aout, septembre, october, novembre, decembre -->
        <div>
          <p>Janvier</p>
          <p>Total d'offre: {{ mois.janvier == null  ? 0 : mois.janvier.length }} </p>
          <p>Total Prix : {{ prix.prixJanvier }} MAD</p>
          <p>Liste des Offre</p>
          <ul v-for="(element, a) in mois.janvier" :key="a">
            <li> {{ element.numeroAffaire }}</li>
          </ul>
        </div>

        <div>
          <p>Février</p>
          <p>Total d'offre: {{ mois.fevrier == null  ? 0 : mois.fevrier.length  }} </p>
          <p>Total Prix : {{ prix.prixFevrier }} MAD</p>
          <p>Liste des Offre</p>
          <ul v-for="(element, b) in mois.fevrier" :key="b">
            <li> {{ element.numeroAffaire }}</li>
          </ul>
        </div>

        <div>
          <p>Mars</p>
          <p>Total d'offre: {{ mois.mars == null  ? 0 : mois.mars.length  }} </p>
          <p>Total Prix : {{ prix.prixMars }} MAD</p>
          <p>Liste des Offre</p>
          <ul v-for="(element, c) in mois.mars" :key="c">
            <li> {{ element.numeroAffaire }}</li>
          </ul>
        </div>

        <div>
          <p>Avril</p>
          <p>Total d'offre: {{ mois.avril == null  ? 0 : mois.avril.length  }} </p>
          <p>Total Prix : {{ prix.prixAvril }} MAD</p>
          <p>Liste des Offre</p>
          <ul v-for="(element, d) in mois.avril" :key="d">
            <li> {{ element.numeroAffaire }}</li>
          </ul>
        </div>

        <div>
          <p>Mai</p>
          <p>Total d'offre: {{ mois.mai == null  ? 0 : mois.mai.length  }} </p>
          <p>Total Prix : {{ prix.prixMai }} MAD</p>
          <p>Liste des Offre</p>
          <ul v-for="(element, c) in mois.mai" :key="c">
            <li> {{ element.numeroAffaire }}</li>
          </ul>
        </div>

        <div>
          <p>Juin</p>
          <p>Total d'offre: {{ mois.juin == null  ? 0 : mois.juin.length  }} </p>
          <p>Total Prix : {{ prix.prixJuin }} MAD</p>
          <p>Liste des Offre</p>
          <ul v-for="(element, d) in mois.juin" :key="d">
            <li> {{ element.numeroAffaire }}</li>
          </ul>
        </div>

        <div>
          <p>Juillet</p>
          <p>Total d'offre: {{ mois.juillet == null  ? 0 : mois.juillet.length }} </p>
          <p>Total Prix : {{ prix.prixJuillet }} MAD</p>
          <p>Liste des Offre</p>
          <ul v-for="(element, e) in mois.juillet" :key="e">
            <li> {{ element.numeroAffaire }}</li>
          </ul>
        </div>

        <div>
          <p>Août</p>
          <p>Total d'offre: {{ mois.aout == null  ? 0 : mois.aout.length  }} </p>
          <p>Total Prix : {{ prix.prixAout }} MAD</p>
          <p>Liste des Offre</p>
          <ul v-for="(element, f) in mois.aout" :key="f">
            <li> {{ element.numeroAffaire }}</li>
          </ul>
        </div>

        <div>
          <p>Septembre</p>
          <p>Total d'offre: {{ mois.septembre == null  ? 0 : mois.septembre.length }} </p>
          <p>Total Prix : {{ prix.prixSeptembre }} MAD</p>
          <p>Liste des Offre</p>
          <ul v-for="(element, j) in mois.septembre" :key="j">
            <li> {{ element.numeroAffaire }}</li>
          </ul>
        </div>

        <div>
          <p>Octobre</p>
          <p>Total d'offre: {{ mois.octobre == null  ? 0 : mois.octobre.length }} </p>
          <p>Total Prix : {{ prix.prixOctober }} MAD</p>
          <p>Liste des Offre</p>
          <ul v-for="(element, k) in mois.octobre" :key="k">
            <li> {{ element.numeroAffaire }}</li>
          </ul>
        </div>

        <div>
          <p>Novembre</p>
          <p>Total d'offre: {{ mois.novembre == null  ? 0 : mois.novembre.length }} </p>
          <p>Total Prix : {{ prix.prixNovembre }} MAD</p>
          <p>Liste des Offre</p>
          <ul v-for="(element, l) in mois.novembre" :key="l">
            <li> {{ element.numeroAffaire }}</li>
          </ul>
        </div>

        <div>
          <p>Décembre</p>
          <p>Total d'offre: {{ mois.decembre == null  ? 0 : mois.decembre.length }} </p>
          <p>Total Prix : {{ prix.prixDecembre }} MAD</p>
          <p>Liste des Offre</p>
          <ul v-for="(element, m) in mois.decembre" :key="m">
            <li> {{ element.numeroAffaire }}</li>
          </ul>
        </div>

      </div>
  </div>
</template>

<script>
import Service from "../../../../Service";
export default {
  name : "indicateur",
  data() {
    return {
        mounths : ["Janvier", "Février", "Mars", "Avril", "Mai", "Juin", "Juillet", "Août", "Septembre", "Octobre", "Novembre", "Décembre"],
        years : [],
        annee : null,
        mois : {
          janvier : null,
          fevrier : null,
          mars : null,
          avril : null,
          mai : null,
          juin : null,
          juillet : null,
          aout : null,
          septembre : null,
          octobre : null,
          novembre : null,
          decembre : null
        },

        prix : {
           prixJanvier : null,
           prixFevrier : null,
           prixMars : null,
           prixAvril : null,
           prixMai : null,
           prixJuin : null,
           prixJuillet : null,
           prixAout : null,
           prixSeptembre : null,
           prixOctober : null,
           prixNovembre : null,
           prixDecembre : null
        }
    }
  },

  methods : {

      indicateur() {
        Service.IndicteurAffaire(this.annee)
          .then((result) => {
            this.mois = result.data;
            this.prix = result.data;
          })
          .catch((error) => {
              console.log(error.message)
          });
      }
  },

  created() {

      // get year anee current
      const today = new Date();
      this.selectAnnee = today.getUTCFullYear();

      for(let i = this.selectAnnee; i >= 1970 ; i--) {
            this.years.push(i);
      }

      // get info Indicateur by current
      Service.IndicteurAffaire(this.selectAnnee)
      .then((result) => {
            this.mois = result.data;
            this.prix = result.data;
      })
      .catch((error) => {
          console.log(error.message)
      });
  }

}
</script>

<style scoped>
.indicateur {
  width: 100%;
  display: flex;
  flex-direction: column;
}

.indicateur > .titre {
  width: 100%;
  background-color: white;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
}

.indicateur > .titre > svg {
  color: #382092;
  font-size: 25px;
  margin-left: 10px;
  margin-right: 10px;
}

.indicateur > .titre > h1 {
  color: #382092;
  margin-left: 10px;
  margin-right: 10px;
}

.indicateur > .titre > h1 > span:nth-child(1) {
    color: red;
    border-top: 5px solid #382092;
}

.indicateur > .titre > h1 > span:nth-child(2) {
    color: #382092;
    border-bottom: 5px solid red;
}

.indicateur > .text {
  color: #382092;
  margin-left: 10px;
  margin-right: 10px;
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: white;
  padding: 35px;
  margin: 0px;
}

.indicateur > .text > h2 {
    font-size: 25px;
    background-color: #382092;
    color: white;
    margin: 0;
    padding: 15px;
    border-radius: 10px 10px 0px 0px;
    border-bottom: 3px solid red;
}

.indicateur > .text > h2 svg {
    font-size: 25px;
    margin-right: 10px;
}


.indicateur > .text > span {
    color: #ddd;
    font-size: 25px;
    background-color: #382092;
    padding: 30px;
}

.indicateur > .filter {
    width: 100%;
    background-color: white;
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
}



.indicateur > .filter > select {
    height: 50px;
    width: 400px;
    margin-left: 10px;
}

.indicateur > .redirect {
  background-color: white;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  padding-top: 15px;
  padding-bottom: 15px;
}

.indicateur > .redirect > svg {
    font-size: 40px;
    margin-right: 10px;
    color: #382092;
}

.indicateur > .statstique {
  display: grid;
  grid-template-columns: repeat(6, 1fr);
  grid-template-rows: repeat(2, 1fr);
  grid-column-gap: 0px;
  grid-row-gap: 0px;
}


.indicateur > .statstique > div > p > svg{
    color: #382092;
    font-size: 25px;
}
.indicateur > .statstique > div > p:nth-child(2){
    color: #4e4e4e;
}

#app > div > div > div.menu-content > div.content > div > div.statstique > div > ul {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: flex-start;
  flex-wrap: nowrap;
}

#app > div > div > div.menu-content > div.content > div > div.statstique > div > ul > li {
  font-size: 15px;
}

</style>