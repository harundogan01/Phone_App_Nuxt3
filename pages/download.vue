<template>
  <v-card class="mx-auto">
    <v-card-text v-if="state.no_data !== ''">
      {{ state.no_data }}
    </v-card-text>
    <v-card-text v-else>
      <v-btn color="error" @click="downloadFile">Download File</v-btn>
    </v-card-text>
  </v-card>
</template>

<script setup>
import { onBeforeMount, reactive } from "vue";
import { POSITION, useToast } from "vue-toastification";
import axios from "axios";
const data = {
  no_data: "",
  file: [],
};
const state = reactive({
  ...data,
});
const toast = useToast();
const phones = onBeforeMount(() => {
  axios.get("http://localhost:3000/data").then((response) => {
    if (response.data.length == 1 || response.data.length == 0)
      state.no_data = "No phones have been added. Please add a new phone.";
    else {
      response.data.forEach((val, index) => {
        data.file.push(val);
      });
    }
  });
});
const downloadFile = () => {
  const jsonData = JSON.stringify(data.file);
  const blob = new Blob([jsonData], { type: "application/json" });
  const url = URL.createObjectURL(blob);

  const link = document.createElement("a");
  link.href = url;
  link.download = "data.json";

  link.click();

  URL.revokeObjectURL(url);
  toast.success("File download completed successfully.", {
    timeout: 3000,
    position: POSITION.TOP_CENTER,
  });
};
</script>

<style></style>
