<!-- 
  Copy paste your code from the ListPlayer.vue file here from the previous exercise.

  In addition to the code from the previous exercise, you need to add the following logic inside the SelectedPlayer component:

  - Display the player's id in an element with the class attribute "player-id".

  - Inside the element with the id "player-status", add a label with the text "active" or "inactive" depending on the status of the player. The label should have an id of "checkbox-label", and inside it there should be a checkbox with an id of "checkbox". 
      - By default, the checkbox should be checked if the player is active and unchecked if the player is inactive. Unlike in elm exercises, toggling the checkbox should not automatically update the player in the backend, instead it is done by the update button (check next point). For styling purposes, add an empty span with the class attribute "checkmark" inside the label. Much like in the elm exercises, the checkbox should be listening to the change event. 

  - Add an update button with the class attribute "btn-update". The button should be disabled if the current active state of the checkmark is not different from the players "isActive" state. Add logic to send the "put-player" event when the button is clickable and the user clicks it. The event should pass the players "isActive" state as a parameter.

  - Add a delete button with the class attribute "btn-delete". Add logic to send the "delete-player" event when the user clicks the button. The event should pass the id of the player as a parameter.

 -->

 <template>
  <div v-if="player" :id="'selected-player'">
    <div :id="'player-id'">{{ player.id }}</div>
    <div :id="'player-name'">{{ player.name }}</div>
    <div :id="'player-status'">
      <label :for="'checkbox'" :id="'checkbox-label'">
        <input
          type="checkbox"
          id="checkbox"
          v-model="isActive"
          @change="updateCheckbox"
        />
        <span class="checkmark"></span>
        {{ isActive ? 'active' : 'inactive' }}
      </label>
    </div>
    <button
      class="btn-update"
      :disabled="!isCheckboxStateChanged"
      @click="updatePlayer"
    >
      Update
    </button>
    <button class="btn-delete" @click="deletePlayer">Delete</button>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  props: {
    player: {
      type: Object,
      default: null,
    },
  },
  data() {
    return {
      isActive: this.player ? this.player.isActive : false,
      isCheckboxStateChanged: false,
    };
  },
  watch: {
    player: {
      handler(newPlayer) {
        this.isActive = newPlayer ? newPlayer.isActive : false;
      },
      immediate: true,
    },
    isActive() {
      this.isCheckboxStateChanged = true;
    },
  },
  methods: {
    updateCheckbox() {
      this.isCheckboxStateChanged = true;
    },
    async updatePlayer() {
      if (this.isCheckboxStateChanged) {
        try {
          const updatedPlayer = { ...this.player, isActive: this.isActive };
          await axios.put(`http://localhost:3001/api/players/${updatedPlayer.id}`, updatedPlayer);
          this.$emit('put-player', this.isActive);
          this.isCheckboxStateChanged = false;
        } catch (error) {
          console.error("Error updating player:", error);
        }
      }
    },
    deletePlayer() {
      this.$emit('delete-player', this.player.id);
    },
  },
};
</script>


