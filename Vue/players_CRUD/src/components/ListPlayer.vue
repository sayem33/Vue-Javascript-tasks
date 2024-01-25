<!-- 
  Copy paste your code from the ListPlayer.vue file here from the previous exercise. No changes are needed if you managed to get the component to work properly in the previous exercise.
 -->

 <template>
  <li :id="'player-' + player.id">
    <a
      href="#"
      @click.prevent="handlePlayerClick"
      tabindex="0"
    >
      {{ player.name }}
    </a>
  </li>
</template>

<script>
export default {
  props: {
    player: {
      type: Object,
      required: true,
    },
  },
  methods: {
    handlePlayerClick() {
      this.$emit('player-clicked', this.player.id);
    },
  },
};
</script>

