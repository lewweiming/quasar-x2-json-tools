<!--
This component must manually update the mutableObject on changes to the database
- We've avoid using Q-table as it adds unnecessary complexity
-->

<template>
  <div class="text-white">

    <div v-for="(r, index) in mutableObject" class="row">
        <div>
            <q-btn @click="deleteItem(index)" icon="delete" />
            <q-input @change="updateItem(index, $event)" debounce=500 filled class="inline" dark v-model="mutableObject[index]" />
        </div>

    </div>
    <div class="q-my-md">
    <q-btn @click="addItem()" size="sm" no-caps dense icon="add" color="text-white" glossy />
    </div>
  </div>
</template>

<script>
export default {
  props: {
    jsonObject: Object,
  },
  watch: {
    jsonObject(val) {
        this.mutableObject = [...val]
    }
  },
  data() {
    return {
        mutableObject: [...this.jsonObject]
    }
  },
  methods: {
      deleteItem(index) {
          this.mutableObject.splice(index, 1)
          this.$emit('delete', index)
      },
      addItem() {
          this.mutableObject.push('New row')
        this.$emit('add')
      },
      updateItem(index, val) {
          this.$emit('update', {
              index: index,
              val: val
          })
      }
  }
};
</script>