<template>
  <div id="app" class="mb-3">
    <div class="container">
      <h1 class="my-3">Welcome to JSON parser</h1>
      <b-form-textarea
        id="textarea"
        v-model="text"
        placeholder="Поместите сюда JSON..."
        rows="8"
        max-rows="15"
        @input="processJson" />

      <b-row
        v-for="(node, index) in nodes"
        :key="index">
        <b-col>{{node}}</b-col>
      </b-row>
      
      <b-row v-if="error != null">
        {{ error }}
      </b-row>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      text: null,
      nodes: [],
      error: null
    }
  },
  methods: {
    processJson() {
      try {
        this.nodes = []
        let nodes = JSON.parse(this.text)
        this.getPaths('$', nodes)

        this.error = null
      } catch (e) {
        this.error = e
      }
    },
    getPaths(currentKey, nodes) {
      if (nodes == null) {
        this.nodes.push(`${currentKey} (unknown, null received)`)
        return
      }
      if (typeof(nodes) != 'object') {
        if (typeof(nodes) == 'number') {
          if (nodes.toString().indexOf('.') == -1 || nodes.toString().indexOf(',') == -1) {
            this.nodes.push(`${currentKey} (Int)`)
          } else {
            this.nodes.push(`${currentKey} (Double)`)
          }
        } else {
          this.nodes.push(`${currentKey} (${typeof(nodes)})`)
        }
      } else {
        if (Array.isArray(nodes)) {
          this.nodes.push(`${currentKey} (Array)`)
        } else {
          this.nodes.push(`${currentKey} (Object)`)          
        }
        Object.keys(nodes).map(x => this.getPaths(`${currentKey}.${x}`, nodes[x]))
      }
    }
  }
}
</script>
