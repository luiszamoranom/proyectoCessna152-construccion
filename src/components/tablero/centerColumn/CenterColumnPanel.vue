<template>
  <div class="centerColumnPanel">
    <CarburetorHeat />
    <TrottleKnob />
    <MixtureKnob />
    <WingFlaps />
    <TrimWhell />
  </div>
</template>

<script>
  import CarburetorHeat from './CarburetorHeat.vue'
  import MixtureKnob from './MixtureKnob.vue'
  import TrimWhell from './TrimWheel.vue'
  import WingFlaps from './WingFlaps.vue'
  import TrottleKnob from './TrottleKnob.vue'

  export default {
    components: {
      CarburetorHeat,
      MixtureKnob,
      TrottleKnob,
      TrimWhell,
      WingFlaps,
    },
  }
</script>
