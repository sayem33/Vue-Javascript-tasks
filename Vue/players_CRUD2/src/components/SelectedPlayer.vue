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
  <div v-if="player" id="selected-player">
    <div class="player-id">{{ player.id }}</div>
    <div id="player-name">{{ player.name }}</div>
    
    <div id="player-status">
      {{ checkboxState ? 'active' : 'inactive' }}
      <label id="checkbox-label">
        <input id="checkbox" type="checkbox" v-model="checkboxState" @change="handleCheckboxChange" />
        <span class="checkmark"></span>
    </label>
    </div>  

    <button class="btn-update" :disabled="player.isActive === checkboxState" @click="updatePlayer">Update</button>
    <button class="btn-delete" @click="deletePlayer">Delete</button>
  </div>
</template>

<script>
export default {
  props: {
    player: {
      type: Object,
      default: null,
    },
  },

  data() {
    return {
      checkboxState: this.player ? this.player.isActive : false,
    };
  },

  methods: {
    handleCheckboxChange() {
      this.checkboxState = !this.checkboxState;
    },

    updatePlayer() {
      if (this.player.isActive !== this.checkboxState) {
        this.$emit('put-player', this.checkboxState);
      }
    },
    

    deletePlayer() {
      this.$emit('delete-player', this.player.id);
    },
  },

  watch: {
    player: {
      handler(newval) {
        this.checkboxState = newval ? newval.isActive : false;
      },
      immediate: true,
    },
  },
};
</script>
