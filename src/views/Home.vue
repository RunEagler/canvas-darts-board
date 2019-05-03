<template>
  <div class="home">
    <DartsBoard></DartsBoard>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import DartsBoard from '@/components/DartsBoard'

@Component({
  components: {
      DartsBoard
  },
})
export default class Home extends Vue {}
</script>
