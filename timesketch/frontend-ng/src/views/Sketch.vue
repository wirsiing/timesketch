<!--
Copyright 2019 Google Inc. All rights reserved.

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
  <router-view v-if="sketch.status"></router-view>
</template>

<script>
export default {
  props: ['sketchId'],
  created: function () {
    this.$store.dispatch('updateSketch', this.sketchId)
    this.$store.dispatch('updateSearchHistory', this.sketchId)
    this.$store.dispatch('updateScenario', this.sketchId)
    this.$store.dispatch('updateSigmaList', this.sketchId)
  },
  computed: {
    sketch() {
      return this.$store.state.sketch
    },
  },
  watch: {
    sketch: function (newVal) {
      if (newVal.status[0].status === 'archived') {
        this.$router.push({ name: 'Overview', params: { sketchId: this.sketch.id } })
      }
      document.title = this.sketch.name
    },
  },
}
</script>
