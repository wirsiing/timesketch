<!--
Copyright 2022 Google Inc. All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->
<template>
  <div>
    <v-toolbar dense flat style="background: transparent">
      <v-btn-toggle v-model="toggleStateButton" class="ml-n4">
        <v-btn text small color="primary" @click="switchScope('user')"> My sketches </v-btn>
        <v-btn text small color="primary" @click="switchScope('shared')"> Shared with me </v-btn>
        <v-btn text small color="primary" @click="switchScope('recent')"> Recent </v-btn>
      </v-btn-toggle>
      <v-btn depressed small color="success" class="ml-3"> New sketch </v-btn>
      <v-spacer></v-spacer>
      <v-text-field
        class="mr-n4"
        solo
        hide-details
        single-line
        label="Search"
        prepend-inner-icon="mdi-magnify"
        @change="search"
      ></v-text-field>
    </v-toolbar>
    <br />
    <v-data-table
      :headers="headers"
      :items="sketches"
      :items-per-page="15"
      :options.sync="options"
      :server-items-length="numSketches"
      :footer-props="{
        'items-per-page-options': [15, 30, 40, 50, 100],
      }"
      disable-filtering
      disable-sort
      :loading="loading"
    >
      <template v-slot:item.name="{ item }">
        <router-link style="text-decoration: none" :to="{ name: 'Overview', params: { sketchId: item.id } }">{{
          item.name
        }}</router-link>
      </template>
      <template v-slot:item.created_at="{ item }">
        {{ item.created_at | shortDateTime }} <small>({{ item.created_at | timeSince }})</small>
      </template>
      <template v-slot:item.last_activity="{ item }">
        {{ item.last_activity | timeSince }}
      </template>
    </v-data-table>
  </div>
</template>

<script>
import ApiClient from '../utils/RestApiClient'

export default {
  data() {
    return {
      headers: [
        {
          text: 'Name',
          align: 'start',
          value: 'name',
        },
        { text: 'Creator', value: 'user' },
        { text: 'Created at', value: 'created_at' },
        { text: 'Last active', value: 'last_activity' },
      ],
      sketches: [],
      numSketches: 0,
      options: {},
      loading: false,
      toggleStateButton: 0,
      scope: 'user',
      searchQuery: null,
    }
  },
  watch: {
    options: {
      handler() {
        this.getSketches()
      },
      deep: true,
    },
  },
  methods: {
    search: function (query) {
      this.scope = 'search'
      this.searchQuery = query
      this.getSketches()
    },
    switchScope: function (newScope) {
      this.scope = newScope
      this.getSketches()
    },
    getSketches: function () {
      this.loading = true
      ApiClient.getSketchList(this.scope, this.options.page, this.options.itemsPerPage, this.searchQuery)
        .then((response) => {
          this.sketches = response.data.objects
          this.numSketches = response.data.meta.total_items
          this.loading = false
        })
        .catch((e) => {
          this.loading = false
          console.error(e)
        })
    },
  },
}
</script>

<!-- CSS scoped to this component only -->
<style scoped lang="scss"></style>
