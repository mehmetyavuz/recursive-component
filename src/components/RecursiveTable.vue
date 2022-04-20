<template>
  <table v-if="records && records.length > 0" class="table table-sm table-bordered">
    <thead>
    <tr class="table-light">
      <th></th>
      <th v-for="key in getDataKeys(records[0].data)" :key="key"> {{ key }}</th>
      <th></th>
    </tr>
    </thead>

    <tbody>
    <template v-for="(item, i) in this.records" :key="item.data.custom_id">
      <tr>
        <td>
          <button v-if="hasKids(item.kids)"
                  @click="toggleRow(item.data)"
                  class="btn btn-outline-success btn-sm" :key="item.data.custom_id">
            <template v-if="item.data.is_shown">Hide</template>
            <template v-else>Show</template>
          </button>
        </td>
        <td v-for="d in getData(item.data)" :key="d.custom_id">
          {{ d }}
        </td>
        <td>
          <button @click="this.$emit('remove-record', item)"
                  class="btn btn-outline-danger btn-sm"> X
          </button>
        </td>
      </tr>

      <tr v-if="Object.keys(item.kids).length > 0" v-show="item.data.is_shown"
          :key="item.data.custom_id"
          :id="'accordion-' + item.data.custom_id" >
        <td :colspan="Object.keys(item.data).length + 1"
            :style="'padding-left:' + (level + 1) * 2 + 'em' ">
          <div>
            <span v-if="item.kids[getKey(item.kids)].records.length > 0" class="float-start small">
              {{ getKey(item.kids).toUpperCase() }}
            </span>
            <RecursiveTable @remove-record="this.removeRecord" :row="i" :level="level+1"
                            :records="item.kids[getKey(item.kids)].records"></RecursiveTable>
          </div>
        </td>
      </tr>
    </template>
    </tbody>
  </table>
</template>

<script>
export default {
  name: 'RecursiveTable',
  props: ['row', 'level', 'records'],
  methods: {
    getDataKeys(data) {
      return Object.keys(data).filter(x => x !== 'custom_id')
    },
    getData(data) {
      let newData = {}
      Object.keys(data).filter(key => {
        if (key !== 'custom_id')
          newData[key] = data[key]
      })
      return newData
    },
    removeRecord(record) {
      this.$emit('remove-record', record)
    },
    toggleRow(id) {
      id.is_shown = !id.is_shown
    },
    getKey(kids) {
      return Object.keys(kids)[0]
    },
    hasKids(obj) {
      return Object.keys(obj).length > 0
    },
  }
}
</script>