<template lang="pug">
#q-app
  q-card
    q-tabs.text-grey(
      v-model="tab",
      dense,
      active-color="primary",
      indicator-color="primary",
      align="justify",
      narrow-indicator
    )
      q-tab(name="Vorgaben", label="Vorgaben")
      //-q-tab(name="Zeitleiste", label="Zeitleiste")
  q-separator
  q-tab-panels(v-model="tab", animated)
    q-tab-panel(name="Vorgaben")
      Vorgaben
    //-q-tab-panel(name="Zeitleiste")
      Zeitleisten
</template>

<script>
import Vorgaben from './components/Vorgaben'
//-import Zeitleisten from './components/Zeitleisten'
export default {
  name: 'App',
  components: { Vorgaben },
  data: () => ({
    tab: 'Vorgaben',
  }),
  methods: {
  }
}
</script>
