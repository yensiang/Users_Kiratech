<template>
  <q-page>
    <div class="row q-py-lg items-start">
      <div class="col-2"></div>
      <div class="col-2">
        <q-img src="~assets/Logo.png" />
      </div>
    </div>
    <div
      style="background: linear-gradient(180deg, lightskyblue 80%, #ffffff 20%)"
      class="row q-pt-xl"
    >
      <div class="col-2"></div>
      <div class="col-2">
        <q-img src="~/assets/Avatar.png"> </q-img>
      </div>
      <div class="col-2 column q-col-gutter-y-md">
        <div
          class="col-6 row justify-center items-end text-h3 text-white text-bold"
        >
          John Doe
        </div>
        <div class="col-6 row justify-center items-start text-h6 text-white">
          Last online : 2 days ago
        </div>
      </div>
      <div class="col-4 row items-center q-gutter-lg">
        <q-btn
          flat
          text-color="info"
          style="background: white"
          class="q-px-xl q-py-md"
          icon="send"
          label="Send Message"
        ></q-btn>
        <q-btn
          outline
          class="q-px-xl q-py-md text-white"
          icon="add"
          label="Add Friend"
        ></q-btn>
      </div>
    </div>
    <div class="row">
      <div class="col-2"></div>
      <q-card class="col-10">
        <q-table
          :rows="rows"
          :columns="columns"
          :loading="loading"
          row-key="name"
          v-model:pagination="pagination"
          @request="onRequest"
        />
      </q-card>
    </div>
  </q-page>
</template>

<script>
export default {
  name: "IndexPage",
  inheritAttrs: false,
  customOptions: {},
};
</script>

<script setup>
import { ref, onMounted, computed } from "vue";
import { api } from "boot/axios";

let loading = ref(false); //Variable to show loading when user changes page or changes number of rows
const columns = [
  { name: "date", align: "left", label: "Date", field: "date" },
  { name: "name", align: "left", label: "Name", field: "name" },
  { name: "gender", align: "center", label: "Gender", field: "gender" },
  { name: "country", align: "center", label: "Country", field: "country" },
];
let pagination = ref({
  page: 1,
  rowsPerPage: 5,
  rowsNumber: 100,
});
let table_data = ref([]); // container to receive data from api

// mapping data from server into desired format before feeding info table
let rows = computed(() => {
  return table_data.value.length > 0
    ? table_data.value.map((user) => {
        return {
          name: `${user.name.title} ${user.name.first} ${user.name.last}`,
          date: user.dob.date,
          gender: user.gender,
          country: user.location.country,
        };
      })
    : [];
}); //initially rows will be empty when data hasnt been fetched from api

// Fetch rows from api
function fetchFromServer(page, pageNum) {
  console.log(`https://randomuser.me/api/?page=${page}&results=${pageNum}`);
  api
    .get(`https://randomuser.me/api/?page=${page}&results=${pageNum}`)
    .then((response) => {
      table_data.value = response.data.results;
      pagination.value.page = response.data.info.page;
      pagination.value.rowsPerPage = response.data.info.results;
      loading.value = false;
    })
    .catch((err) => {
      // $q.notify({
      //   color: "negative",
      //   position: "top",
      //   message: err.response.data,
      // });
    });
}

// Function is called everytime user changes page or selects number of rows to show
function onRequest(props) {
  const { page, rowsPerPage, rowsNumber } = props.pagination;

  loading.value = true;

  // emulate server

  // fetch data from "server"
  fetchFromServer(page, rowsPerPage);

  // // don't forget to update local pagination object
}

// Fetch rows from server
onMounted(() => {
  fetchFromServer(pagination.value.page, pagination.value.rowsPerPage);
  // api
  //   .get("https://randomuser.me/api/?results=20.")
  //   .then((response) => {
  //     table_data.value = response.data.results;
  //   })
  //   .catch((err) => {
  //     // $q.notify({
  //     //   color: "negative",
  //     //   position: "top",
  //     //   message: err.response.data,
  //     // });
  //   });
});
</script>
