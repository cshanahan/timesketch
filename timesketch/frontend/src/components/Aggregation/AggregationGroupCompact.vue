<!--
Copyright 2020 Google Inc. All rights reserved.

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
  <div class="card">
    <div class="card-content" ref="vegaChart">
      <ts-vega-lite-chart :vegaSpec="vegaSpec"></ts-vega-lite-chart>
    </div>
  </div>
</template>

<script>
import ApiClient from '../../utils/RestApiClient'
import TsVegaLiteChart from './VegaLiteChart'

export default {
  props: ['aggregation_group'],
  components: { TsVegaLiteChart },
  data() {
    return {
      vegaSpec: {},
      title: '',
    }
  },
  computed: {
    sketch() {
      return this.$store.state.sketch
    },
  },
  methods: {
    getVegaSpec: function() {
      ApiClient.runAggregatorGroup(this.sketch.id, this.aggregation_group.id)
        .then(response => {
          let spec = response.data.meta.vega_spec
          spec.config.view.width = this.$refs.vegaChart.offsetWidth - 50
          spec.config.autosize = { type: 'fit', contains: 'padding' }
          this.vegaSpec = JSON.stringify(spec)
          this.title = response.data.meta.vega_chart_title
        })
        .catch(e => {})
    },
  },
  mounted: function() {
    this.getVegaSpec()
  },
}
</script>
