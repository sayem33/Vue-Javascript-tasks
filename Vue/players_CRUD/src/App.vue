<!-- 
  COPY AND PASTE YOUR CODE FROM THE PREVIOUS EXERCISE HERE. Also implement the following:

  1. Create a method for adding a new player. This method should handle the logic for adding a new player to the database and updating the players array. This method should also reset the form only if the request was successful. This method should be called when the add player form is submitted.

  2. Create a method for deleting a player. This method should handle the logic for deleting a player from the database and updating the players array. This method should be called when the delete button is clicked in the SelectedPlayer component.

  3. Create a method for updating a player. This method should handle the logic for updating a player in the database and updating the players array. This method should be called when the update button is clicked in the SelectedPlayer component.
 -->
 <template>
  <div>
    <h2>Request status</h2>
    <RequestStatus :status="reqStatus" />
    <h2>Add Player</h2>
    <AddPlayer @add-player="addPlayer" />
  </div>
  <div>
    <h2>List of players</h2>
    <ListPlayers :players="players" :getPlayer="fetchPlayer" @add-player="addPlayer" @delete-player="deletePlayer" @update-player="updatePlayer" />
  </div>
  <div>
    <h2>Selected player</h2>
    <SelectedPlayer v-if="selectedPlayer" :player="selectedPlayer" @delete-player="deletePlayer" @update-player="updatePlayer" />
  </div>
</template>

<script>
import ListPlayers from "./components/ListPlayers.vue";
import SelectedPlayer from "./components/SelectedPlayer.vue";
import RequestStatus from "./components/RequestStatus.vue";
import AddPlayer from "./components/AddPlayer.vue"; 
import axios from 'axios';

const REQ_STATUS = {
  loading: "Loading...",
  success: "Finished!",
  error: "An error has occurred!!!",
};

export default {
  components: {
    RequestStatus,
    ListPlayers,
    SelectedPlayer,
    AddPlayer,
  },
  data() {
    return {
      players: [],
      selectedPlayer: null,
      reqStatus: null,
    };
  },
  async created() {
    await this.fetchPlayers();
  },
  methods: {
    async fetchPlayers() {
      this.reqStatus = REQ_STATUS.loading;
      try {
        const response = await axios.get('http://localhost:3001/api/players');
        this.players = response.data;
        console.log("response.data",response.data)
        this.reqStatus = REQ_STATUS.success;
      } catch (error) {
        console.error("Error fetching players:", error);
        this.reqStatus = REQ_STATUS.error;
      }
    },
    async fetchPlayer(id) {
      this.reqStatus = REQ_STATUS.loading;
      try {
        const response = await axios.get(`http://localhost:3001/api/players/${id}`);
        this.selectedPlayer = response.data;
        this.isActive = this.selectedPlayer.isActive;
        console.log("selectedPlayer",this.selectedPlayer)
        console.log("ID",response.data)
        this.reqStatus = REQ_STATUS.success;
      } catch (error) {
        console.error("Error fetching player:", error);
        this.reqStatus = REQ_STATUS.error;
      }
    },
    async addPlayer(newPlayerName) {
      this.reqStatus = REQ_STATUS.loading;
      try {
        const response = await axios.post('http://localhost:3001/api/players', { name: newPlayerName });
        this.players.push(response.data);
        this.reqStatus = REQ_STATUS.success;
      } catch (error) {
        console.error("Error adding player:", error);
        this.reqStatus = REQ_STATUS.error;
      }
    },
    async deletePlayer(id) {
      this.reqStatus = REQ_STATUS.loading;
      try {
        await axios.delete(`http://localhost:3001/api/players/${id}`);
        this.players = this.players.filter(player => player.id !== id);
        this.selectedPlayer = null;
        this.reqStatus = REQ_STATUS.success;
      } catch (error) {
        console.error("Error deleting player:", error);
        this.reqStatus = REQ_STATUS.error;
      }
    },
    async updatePlayer(isActive) {
      if (this.selectedPlayer) {
        this.reqStatus = REQ_STATUS.loading;
        try {
          const updatedPlayer = { ...this.selectedPlayer, isActive };
          await axios.put(`http://localhost:3001/api/players/${this.selectedPlayer.id}`, updatedPlayer);
          this.players = this.players.map(player => (player.id === this.selectedPlayer.id ? updatedPlayer : player));
          this.reqStatus = REQ_STATUS.success;
        } catch (error) {
          console.error("Error updating player:", error);
          this.reqStatus = REQ_STATUS.error;
        }
      }
    },
  },
};
</script>

<style></style>


