<template>
  <v-app>
    <v-main>
      <home @openDialog="dialog = true" />

      <v-dialog v-modal="dialog" fullscreen> </v-dialog>

      <div style="text-align: center">
        <a href="https://github.com/eoinoreilly30/chat-maps" target="_blank"
          >Made with &#10084;</a
        >
      </div>
    </v-main>
  </v-app>
</template>

<script>
import Home from "@/components/Home";
export default {
  name: "App",

  components: {
    Home
  },

  data: () => ({
    dialog: false
  })
};
</script>
