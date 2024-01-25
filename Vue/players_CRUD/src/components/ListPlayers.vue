<!-- 
  Copy paste your code from the ListPlayer.vue file here from the previous exercise. No changes are needed if you managed to get the component to work properly in the previous exercise.
 -->

 <template>
  <ul :id="'players-list'">
    <ListPlayer
      v-for="player in players"
      :key="player.id"
      :player="player"
      @player-clicked="getPlayer"
    />
  </ul>
</template>

<script>
import ListPlayer from './ListPlayer.vue';

export default {
  components: {
    ListPlayer,
  },
  props: {
    getPlayer: {
      type: Function,
      required: true,
    },
    players: {
      type: Array,
      required: true,
    },
  },
};
</script>
