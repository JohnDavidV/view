<template>
  <v-app>
    <v-main>
      <RegistroPersona/>
    </v-main>
  </v-app>
</template>

<script>
import RegistroPersona from './components/RegistroPersona';

export default {
  name: 'App',

  components: {
    RegistroPersona,
  },

  data: () => ({
    //
  }),
};
</script>
