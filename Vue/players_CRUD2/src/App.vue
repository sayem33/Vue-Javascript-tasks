<!-- 
  COPY AND PASTE THE CODE FROM THE PREVIOUS EXERCISE, BUT:
  - Beware, the template is different: AuthUser is now a child of the root div element. When copy-pasting the logic to the new template, make sure to add the AuthUser component back in. 
  - You are no longer automatically fetching the players every time the App is rendered. Instead, you should only fetch the players when the user is logged in successfully.

  What is the function of the AuthUser component in the big picture?
  - Depending on the state of the AuthUser component, the other components should be displayed or hidden (except for the RequestStatus component, which is always visible). If the user is logged in, the AddPlayer, ListPlayers, and SelectedPlayer components should be displayed. If the user is not logged in, only the AuthUser component and the RequestStatus component should be displayed.

  1. Inside the root div element, give the AuthUser the appropriate props and event listeners. It should emit the "login", "register", and "logout" events. You need to give it the "isLoggedIn" prop, which is used to determine the state of the AuthUser component. If you removed the AuthUser componenet because you overwrote the whole template with the new one, remember to add it back in.

  2. Create a method for registering a user when the AuthUser component emits the "register" event. This method should handle the logic for registering a user. After a successful registration, save the user's username and password into the App's state. 
  
  The backend uses the HTTP Basic auth, which means that the username and password as sent in base64 encoded format in the Authorization header upon every request except for the registration request. 

  The header contents should be of the following format: "Basic <base64 encoded username:password>". The username and password should be separated by a colon. The username and password should be base64 encoded. You can use the btoa() function to encode the username and password. For example, if the username is "user" and the password is "password", the header could be generated with the following code: `Basic ${window.btoa(`user:password`)}`; 

  The backend will respond with 401 if the Authorization header is missing, and with a status of 403 if the credentials are invalid. 

  After a succesful registration, the app should attempt to fetch players from the database. If it fails to fetch the players, then the user should stay logged out. If it succeeds, the user should be logged in and the app state should be updated accordingly and the players list should be displayed. Notice that separate login is not required after a successful registration, because the user is already logged in.
  
  3. Create a method for logging in when the AuthUser component emits the "login" event. This method should handle the logic for logging in a user. As described earlier, the backend does not have a separate login endpoint. Instead, the app should try to fetch players from the database with the given credentials using Basic auth. If the request is successful, the user is logged in and the app state should be updated accordingly.

  4. Create a method for logging out when the AuthUser component emits the "logout" event. This method should handle the logic for logging out a user. This method should be called when the logout event is emitted from the AuthUser component. When the user logs out, the application should be reset to its initial state (ergo, remove all data that was fetched from the database)

  HINT: Remember to add the Authorization header to every request except the user registration. 

 -->

 <template>
  <div>
    <h2> Request Status </h2>
    <RequestStatus :status="reqStatus" />
  </div>
  <AuthUser v-if="!isLoggedIn" @login="login" @register="register" @logout="logout" :isLoggedIn="isLoggedIn" />

  <div v-if="isLoggedIn">
    <button @click="logout">Logout</button>
    <h2> Add Player </h2>
    <AddPlayer @add-player="addPlayer"/>
  </div>

  <div>
    <h2> List of players </h2>
    <ListPlayers :players="players" :getPlayer="fetchPlayer" />
  </div>

  <div>
    <h2>Selected player</h2>
    <SelectedPlayer v-if="selectedPlayer" :player="selectedPlayer" @delete-player="deletePlayer" @put-player="updatePlayer"/>
  </div>

</template>

<script>

import AuthUser from './components/AuthUser.vue';
import AddPlayer from './components/AddPlayer.vue';
import ListPlayers from "./components/ListPlayers.vue";
import SelectedPlayer from './components/SelectedPlayer.vue';
import RequestStatus from './components/RequestStatus.vue';

import axios from 'axios';


const REQ_STATUS = {
  loading: "Loading...",
  success: "Finished!",
  error: "An error has occurred!!!",
};

export default {
  components: {
    ListPlayers,
    SelectedPlayer,
    RequestStatus,
    AddPlayer,
    AuthUser
},

  data() {
    return {
      players: [],
      selectedPlayer: null,
      reqStatus: null,
      isLoggedIn: false,
      username: null,
      password: null,
    };
  },

  async created() {
    await this.fetchPlayers();
  },

  methods: {
  async register({username, password}) {
    this.reqStatus = REQ_STATUS.loading;
    try {
      await axios.post('http://localhost:3001/api/users', {username, password});
      this.reqStatus = REQ_STATUS.success;
      this.username = username;
      this.password = password;
      this.isLoggedIn = true;
      await this.fetchPlayers();
    } catch (error) {
      console.error("Error registering user:", error);
      this.reqStatus = REQ_STATUS.error;
    }
  },

  async login({username, password}) {
    this.reqStatus = REQ_STATUS.loading;
    try {
      this.username = username;
      this.password = password;
      await this.fetchPlayers();  // Try to fetch players to check if the login is successful
      this.isLoggedIn = true;
      this.reqStatus = REQ_STATUS.success;
    } catch (error) {
      console.error("Error logging in user:", error);
      this.reqStatus = REQ_STATUS.error;
    }
  },

  logout() {
    this.isLoggedIn = false;
    this.username = null;
    this.password = null;
    this.players = [];
    this.selectedPlayer = null;
  },

  async fetchPlayers() {
    this.reqStatus = REQ_STATUS.loading;
    try {
      const response = await axios.get('http://localhost:3001/api/players', {
        headers: {
          Authorization: `Basic ${window.btoa(`${this.username}:${this.password}`)}`
        }
      });
      this.players = response.data;
      this.reqStatus = REQ_STATUS.success;
    } catch (error) {
      console.error("Error fetching players:", error);
      this.reqStatus = REQ_STATUS.error;
    }
  },

  async fetchPlayer(id) {
    this.reqStatus = REQ_STATUS.loading;
    try {
      const response = await axios.get(`http://localhost:3001/api/players/${id}`, {
        headers: {
          Authorization: `Basic ${window.btoa(`${this.username}:${this.password}`)}`
        }
      });
      this.selectedPlayer = response.data;
      this.reqStatus = REQ_STATUS.success;
    } catch(error) {
      console.error("Error fetching player:", error);
      this.reqStatus = REQ_STATUS.error;
    }
  },

  async addPlayer(playerName) {
    this.reqStatus = REQ_STATUS.loading;
    try {
      const newPlayer = { name: playerName, isActive: false };
      const response = await axios.post('http://localhost:3001/api/players', newPlayer, {
        headers: {
          Authorization: `Basic ${window.btoa(`${this.username}:${this.password}`)}`
        }
      });
      this.players.push(response.data);
      this.reqStatus = REQ_STATUS.success;
    } catch(error) {
      console.error("Error adding player:", error);
      this.reqStatus = REQ_STATUS.error;
    }
  },

  async deletePlayer(id) {
    this.reqStatus = REQ_STATUS.loading;
    try {
      await axios.delete(`http://localhost:3001/api/players/${id}`, {
        headers: {
          Authorization: `Basic ${window.btoa(`${this.username}:${this.password}`)}`
        }
      });
      this.players = this.players.filter((player) => player.id !== id);
      this.reqStatus = REQ_STATUS.success;
    } catch(error) {
      console.error("Error deleting player:", error);
      this.reqStatus = REQ_STATUS.error;
    }
  },

  async updatePlayer(id, updatedPlayer) {
    this.reqStatus = REQ_STATUS.loading;
    try {
      const response = await axios.put(`http://localhost:3001/api/players/${id}`, updatedPlayer, {
        headers: {
          Authorization: `Basic ${window.btoa(`${this.username}:${this.password}`)}`
        }
      });
      const index = this.players.findIndex(player => player.id === id);
      if (index !== -1) {
        this.players.splice(index, 1, response.data);
      }
      this.reqStatus = REQ_STATUS.success;
    } catch(error) {
      console.error("Error updating player:", error);
      this.reqStatus = REQ_STATUS.error;
    }
  },
}
}
</script>