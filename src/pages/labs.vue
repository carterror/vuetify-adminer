<script setup lang="ts" >
import { useFetch } from '@vueuse/core'

import {onMounted} from "vue";
import type {User} from "@/interfaces/user.interface";
import all from "@/pages/[...all].vue";

definePage({
  meta: {
    icon: 'mdi-flask',
    title: 'Labs',
    drawerIndex: 3,
  },
})

const listItems = ref<User[]>();
const loading = ref(false)
const itemsPerPage = ref(5)
const page = ref(1)

async function getData() {
  loading.value = true
  try {
    const res = await fetch("https://randomuser.me/api/?results=12");
    const finalRes = await res.json();
    listItems.value = finalRes['results'].map((user: User) => {
      return {
        photo: user.picture.thumbnail,
        name: user.name.first + ' ' + user.name.last,
        gender: user.gender,
        email: user.email,
        phone: user.phone,
      }
    });
    loading.value = false
  } catch (e) {
    console.error('Error de conexion para el fetch')
    console.log(e)
    loading.value = false
  }

}

onMounted(()=>{
  getData()
})

const customNoDataText = ref('No existen datos')
const search = ref('')
const headers = [
  { title: '', key: 'photo' },
  {
    title: 'Name',
    key: 'name',
  },
  { title: 'Gender', key: 'gender' },
  { title: 'Email', key: 'email' },
  { title: 'Phone', key: 'phone' },
]
const items = ref(listItems)
</script>

<template>
  <v-container fluid>
    <v-row>
      <v-col>
        <v-card flat>
          <v-card-title class="d-flex align-center pe-2">
            <v-icon icon="mdi-account-group"></v-icon> &nbsp;
            Users List

            <v-spacer></v-spacer>

            <v-text-field
              v-model="search"
              prepend-inner-icon="mdi-magnify"
              density="compact"
              label="Search"
              single-line
              flat
              hide-details
              variant="solo-filled"
            ></v-text-field>
          </v-card-title>

          <v-divider></v-divider>
          <v-data-table
            v-model:search="search"
            :items="items"
            :loading="loading"
            loading-text="Loading... Please wait"
            no-data-text="No Match data..."
            items-per-page-text="Items per page"
          >

            <template v-slot:header.phone>
              <div class="text-end">Phone Number</div>
            </template>

            <template v-slot:item.photo="{ item }">
              <v-card class="my-2" elevation="2" rounded>
                <v-img
                  :src="`${item.photo}`"
                  height="64"
                  cover
                ></v-img>
              </v-card>
            </template>

<!--            <template v-slot:item.rating="{ item }">-->
<!--              <v-rating-->
<!--                :model-value="item.rating"-->
<!--                color="orange-darken-2"-->
<!--                density="compact"-->
<!--                size="small"-->
<!--                readonly-->
<!--              ></v-rating>-->
<!--            </template>-->

            <template v-slot:item.gender="{ item }">
              <div class="text-end">
                <v-chip
                  :color="item.gender == 'male' ? 'red' : 'green'"
                  :text="item.gender == 'male' ? 'Male' : 'Female'"
                  class="text-uppercase"
                  label
                  size="small"
                ></v-chip>
              </div>
            </template>


            <template v-slot:no-data>
              <v-alert :value="!loading" color="error" icon="warning">
                Sorry, nothing to display here :(
              </v-alert>
            </template>

          </v-data-table>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>
