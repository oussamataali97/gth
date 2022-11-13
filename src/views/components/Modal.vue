<template>
  <transition name="modal-fade">
  <div class="modal-backdrop">
    <div class="modal">

      <header class="modal-header">
        <slot name="header">
          liste des mission
        </slot>
        <button type="button" class="btn-close" @click="close">
          x
        </button>
      </header>

      <section class="modal-body">
        <slot name="body">
          <ul v-for="(item, index) in calendriers" :key="index">
                <li>Mission N° : {{ index + 1 }}</li>
                <li><i class="fa-solid fa-clipboard"></i>{{ item.titre }}</li>
                <li><i class="fa-solid fa-building"></i>{{ item.client }}</li>
                <li><i class="fa-solid fa-location-dot"></i>{{ item.lieu }}</li>
                <li>Date de mission : {{ new Date(item.date).toLocaleDateString() }}</li>
                <li><i class="fa-sharp fa-solid fa-clock"></i>Horaire : {{ item.horaire[0].start +" - "+ item.horaire[0].fin  }}</li>
                <li v-show="item.valider == true">Mission accordée</li>
                <li v-show="item.valider == false">Mission non accordée</li>
                <li>-----------------------------------</li>
          </ul>
        </slot>
       </section>

      <footer class="modal-footer">
        <button type="button" class="btn-green" @click="close">
          Fermer
        </button>
      </footer>

    </div>
  </div>
 </transition>
</template>

<script>
  export default {
    name: 'Modal',
    props : {
        calendriers : Array
    },

    methods: {
      close() {
        this.$emit('close');
      },
    }
  };
</script>

<style scoped>
  .modal-backdrop {
    position: fixed;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    background-color: rgba(0, 0, 0, 0.3);
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .modal {
    background: #FFFFFF;
    box-shadow: 2px 2px 20px 1px;
    overflow-x: auto;
    display: flex;
    flex-direction: column;
    width: 600px;
  }

  .modal-header,
  .modal-footer {
    padding: 15px;
    display: flex;
  }

  .modal-header {
    position: relative;
    border-bottom: 1px solid #eeeeee;
    color: #3418b3;
    justify-content: space-between;
  }

  .modal-footer {
    border-top: 1px solid #eeeeee;
    flex-direction: column;
    justify-content: flex-end;
  }

  .modal-body {
    position: relative;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: center;
    padding: 20px 10px;
  }

  .modal-body > ul {
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: flex-start;
  }

  .modal-body > ul > li:nth-child(1) {
    color: #3418b3;
  }

  .modal-body > ul > li:nth-child(7) {
    color: green;
  }

  .modal-body > ul > li:nth-child(8) {
    color: red;
  }

  .btn-close {
    position: absolute;
    top: 0;
    right: 0;
    border: none;
    font-size: 20px;
    padding: 10px;
    cursor: pointer;
    font-weight: bold;
    color: #4AAE9B;
    background: transparent;
  }

  .btn-green {
    color: white;
    background: #ff0808;
    border: 1px solid #fd0d0d;
    border-radius: 2px;
    margin-bottom: 10px;
    margin-top: 10px;
    height: 40px;
    cursor: pointer;
  }

  .modal-fade-enter,
  .modal-fade-leave-to {
    opacity: 0;
  }

  .modal-fade-enter-active,
  .modal-fade-leave-active {
    transition: opacity .5s ease;
  }

</style>