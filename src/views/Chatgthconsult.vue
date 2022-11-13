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

          <h3>CHAT GTHCONSULT</h3>

          <!-- start chat -->
          <div class="chat">
            <div class="users">

              <div class="front">
                Contacts

              </div>
              <div class="back">
                <ul v-for="item in filterAdmins" :key="item._id">
                  <li @click="chatContact(item._id)"><i v-if="item.connected == true" class="fa-solid fa-user"
                      style="color :green;"></i><i v-if="item.connected == false" class="fa-solid fa-user"
                      style="color :red;"></i>{{ item.nom + " " + item.prenom }} </li>
                </ul>
              </div>
            </div>

            <div class="content-message">
              <img src="./../assets/istockphoto-1201667381-1024x1024.jpg" style="object-fit: cover;width: 100%; height: 100%;opacity:0.1"
                alt="">
              <div class="content">
                <div v-for="(item, i) in filterChatContent" :key="i">
                  <ul v-for="(element, j) in item.content" :key="j">
                    <div>
                      <li><i class="fa-solid fa-user"></i><i class="fa-solid fa-user"></i> {{ element.name }} :</li>
                      <li>{{ element.message }} </li>
                    </div>
                    <div>
                      <li>{{ new Date(element.date).toLocaleString() }} </li>
                      <li v-if="element.vu"><i class="fa-solid fa-check"><i class="fa-solid fa-check"></i></i></li>
                    </div>
                  </ul>

                </div>
              </div>
              <div class="message">
                <textarea v-model="message" id="" placeholder="Type here ..."></textarea>
                <button>
                  <i class="fa-solid fa-paperclip"></i>


                  <input type="file" value="Envoyer" />
                </button>
                <button type="button" class="btn-send" value="Envoyer" @click="envoyerMessage()"> <i
                    class="fa-solid fa-envelope"></i></button>
              </div>
            </div>
          </div>
          <!-- End chat -->



        </div>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import Service from "../Service";
import Nav from "@/components/Admin/Nav.vue";
import io from 'socket.io-client'
const socket = io("http://localhost:5000");
export default {
  name: "admin",
  data() {
    return {
      currentRoom: null,
      message: null,
      admins: [],
      chatcontent: [],
      currentContact: null,
      nom: null,
      prenom: null,
      matricule: null,
      beneficiare: null
    };
  },

  components: {
    Nav,
  },

  computed: {

    filterAdmins() {
      return this.admins.filter((item) => {
        {
          // delete my user
          if (item._id != this.matricule) {
            return item
          }
        }
      })
    },

    filterChatContent() {
      return this.chatcontent.filter((item) => {
        {
          if (item.chatId == this.currentRoom) {
            return item
          }

        }
      })
    }
  },


  methods: {

    envoyerMessage() {
      Service.EnvoyerMessage(this.currentRoom, this.message, this.matricule)
        .then((result) => {
          console.log(result);
          this.message = "";
        })
        .catch((error) => {
          console.log(error);
        });
    },

    // create room chat
    chatContact(conatactId) {
      this.messages = [];
      this.currentContact = conatactId;
      Service.ChatContact(this.currentContact, this.matricule)
        .then((result) => {
          this.currentRoom = result.msg;
        })
        .catch((error) => {
          console.log(error);
        });
    }

  },

  created() {

    if (!sessionStorage.getItem("token")) {
      this.$router.push("/")
    } else {

      this.nom = sessionStorage.getItem("nom");
      this.prenom = sessionStorage.getItem("prenom");
      this.matricule = sessionStorage.getItem("id");
      this.beneficiare = `${this.nom} ${this.prenom}`;

      // get date cuerrent
      var today = new Date();
      this.jour = String(today.getDate()).padStart(2, '0');
      this.mois = String(today.getMonth() + 1).padStart(2, '0');
      this.annee = today.getFullYear();

      // Socket admins
      socket.on("admins", (msg) => {
        this.admins = [];
        this.admins = msg;
      });

      // Socket admins
      socket.on("chatcontent", (msg) => {
        this.chatcontent = [];
        this.chatcontent = msg;
      });

      // Stream for get Online users
      Service.Online(this.matricule)
        .then((result) => {
          if (result) {
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


.admin .container .menu-content .content .chat {

  height: calc(100vh - 127px);
  display: flex;
  width: 100%;
  flex-direction: row;
}

.admin .container .menu-content .content .chat .users {
  width: 30%;
  height: 100%;
  display: flex;
  flex-direction: column;
  background-color: #f7f7f7;


}

#app>div>div>div.menu-content>div.content>div.chat>div.users>div.front {
color:rgb(66, 66, 66);
  text-align: center;
  text-transform: uppercase;
  padding: 10px;
  font-size: 20px;

  font-weight: 500;
  border-bottom: 1px solid #ddd;
}

#app>div>div>div.menu-content>div.content>div.chat>div.users>div.back {
  overflow-y: auto;
  height: 100%;
}

#app>div>div>div.menu-content>div.content>div.chat>div.users>div.back>ul>li {
  color: #ddd;
  padding: 10px;
  font-size: 18px;
  font-weight: 400;
  cursor: pointer;
}

#app>div>div>div.menu-content>div.content>div.chat>div.users>div.back>ul>li:hover {
  color: white;
  padding: 10px;
  font-size: 22px;
  font-weight: 400;
  cursor: pointer;
  transition: 0.3s;
}

#app>div>div>div.menu-content>div.content>div.chat>div.content-message {
  width: 70%;
  z-index: 10;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
}

#app>div>div>div.menu-content>div.content>div.chat>div.content-message .content {
  overflow-y: auto;
}

#app>div>div>div.menu-content>div.content>div.chat>div.content-message .message {
  width: 100%;
  position: fixed;
  display: flex;
  flex-direction: row;
  padding: 16px;
  align-items: center;
  background-color: #dfdfdf;
  text-align: center;
  position: relative;
  justify-content: flex-end;
}

#app>div>div>div.menu-content>div.content>div.chat>div.content-message .message textarea {
  width: 100%;
  overflow-y: auto;
  border: 0;

  margin-right: 10px;
  border-radius: 10px;
  padding: 5px 70px 5px 20px;
}

#app>div>div>div.menu-content>div.content>div.chat>div.content-message .message textarea:focus-within {
  outline: none;
}

#app>div>div>div.menu-content>div.content>div.chat>div.content-message>div.message>button>input[type=file] {
  position: absolute;
  top: 50%;
  left:50%;
  transform: translate(-50%,-50%);
  cursor: pointer;
  opacity: 0;
}

#app>div>div>div.menu-content>div.content>div.chat>div.content-message>div.message>:nth-child(2) {
  background: #636363;
  padding: 10px;
  margin-right: 10px;
  border: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 50%;
  transition: .3s ease-in;
  position: relative;
  overflow: hidden;
}

#app>div>div>div.menu-content>div.content>div.chat>div.content-message>div.message>:nth-child(2):hover {

  background: #243064;
}


.btn-send {

  background: #243064;
  padding: 10px;

  border: 0;
  border-radius: 50%;
  transition: .3s ease-in;

}

.btn-send:hover {

  background: #cf1f21;


}

.fa-paperclip,
.fa-envelope {
  font-size: 20px;
  color: white;
}


#app>div>div>div.menu-content>div.content>div.chat>div.content-message>div.message>input[type=button]:nth-child(3) {
  background-color: #3085d6;
  color: white;
  font-size: 18px;
  border: 0px;
  cursor: pointer;
}

#app>div>div>div.menu-content>div.content>div.chat>div.content-message>div.content>div>ul>div {
  display: flex;
  flex-direction: row;
  margin-bottom: 10px;
  margin-top: 10px;
  margin-left: 10px;
}

#app>div>div>div.menu-content>div.content>div.chat>div.content-message>div.content>div>ul>div:nth-child(2) {
  margin-bottom: 30px;
}

#app>div>div>div.menu-content>div.content>div.chat>div.content-message>div.content>div>ul>div:nth-child(1)>li:nth-child(1) {
  color: red;
  font-size: 18px;
  font-weight: bold;
}

#app>div>div>div.menu-content>div.content>div.chat>div.content-message>div.content>div>ul>div:nth-child(1)>li:nth-child(2) {
  font-size: 16px;
}

#app>div>div>div.menu-content>div.content>div.chat>div.content-message>div.content>div>ul>div:nth-child(1)>li:nth-child(2) {
  padding-left: 10px;
}

#app>div>div>div.menu-content>div.content>div.chat>div.content-message>div.content>div>ul>div:nth-child(2)>li:nth-child(1) {
  color: #575764ab;
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