<template>
  <RecursiveTable @remove-record="removeRecord"
                  :row="0" :level="0"
                  :records="this.source"/>
</template>

<script>
import  { v4 as uuidv4 } from 'uuid'
import RecursiveTable from './components/RecursiveTable.vue'

export default {
  name: 'App',
  components: {
    RecursiveTable
  },
  data() {
    return {
      source: []
    }
  },
  created() {
    this.getSource().then((r) => {
      this.setIdForEachRecord(r)
      this.source = r
    })
  },
  methods: {
    setIdForEachRecord(records) {
      records.forEach(record => {
        record.data['custom_id'] = uuidv4()
        console.log(record.data.custom_id)
        Object.keys(record.kids).forEach(key => {
          this.setIdForEachRecord(record.kids[key].records)
        })
      })
    },
    removeRecord(record) {
      this.source = this.findRemove(this.source, record)
    },
    findRemove(records, target) {
      let newRecords = []
      if (records) {
        for (let i = 0; i < records.length; i++) {
          const record = records[i]
          let newRecord = {data: {}, kids: {}}
          if (target.data !== record.data) {
            newRecord.data = record.data
            const keys = Object.keys(record.kids)
            if (keys.length > 0) {
              let key = keys[0]
              let rs = this.findRemove(record.kids[key].records, target)
              if (rs.length > 0)
                newRecord.kids[key] = {records: rs}
            }
            newRecords.push(newRecord)
          }
        }
      }
      return newRecords
    },
    getSource() {
      return fetch("/example-data.json")
          .then(res => {
            return res.json()
          })
    }
  }
}
</script>