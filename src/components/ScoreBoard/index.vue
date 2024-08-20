<script lang="ts">
import { Options, Vue } from "vue-class-component";

@Options({
  components: {},
  props: {
    winCount: Number,
    loseCount: Number,
  },
})
export default class ScoreBoard extends Vue {
  winCount!: number;
  loseCount!: number;
}
</script>

<template>
  <section class="score">
    Player <span>{{ winCount }}</span> x <span>{{ loseCount }}</span> Computer
  </section>
</template>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
section.score {
  border-bottom: 1px solid black;
  padding: 24px;
  font-size: 18px;

  span {
    font-weight: bold;
    border: 1px solid black;
    padding: 8px;
  }
}
</style>
