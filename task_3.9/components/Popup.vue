<template>
  <q-menu
    v-model="menuOpen"
    :offset="[0, 10]"
    transition-show="jump-down"
    transition-hide="jump-up"
  >
    <q-list style="min-width: 150px">
      <q-item clickable @click="minimize">
        <q-item-section>Minimize</q-item-section>
      </q-item>
      <q-item clickable @click="maximize">
        <q-item-section>{{ isMaximized ? "Restore" : "Maximize" }}</q-item-section>
      </q-item>
      <q-separator />
      <q-item clickable @click="quitApp" class="text-negative">
        <q-item-section>Exit</q-item-section>
      </q-item>
    </q-list>
  </q-menu>
</template>

<script>
import { ref } from "vue";
const { ipcRenderer } = require("electron");

export default {
    setup() {
        const menuOpen = ref(false);
        const isMaximized = ref(false);

        const minimize = () => {
        ipcRenderer.send("window-controls", "minimize");
        menuOpen.value = false;
        };

        const maximize = () => {
        ipcRenderer.send("window-controls", "toggle-maximize");
        isMaximized.value = !isMaximized.value;
        menuOpen.value = false;
        };

        const quitApp = () => {
        ipcRenderer.send("window-controls", "close");
        };

        return {
        menuOpen,
        isMaximized,
        minimize,
        maximize,
        quitApp,
        };
    },
};
</script>
