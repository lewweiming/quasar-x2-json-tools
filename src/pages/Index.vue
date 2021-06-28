<template>
<div>
  <!-- Dialog -->
<DialogConvert @convert="onConvert($event)" :show="showDialog" @close="showDialog = false" />


  <q-splitter v-model="splitterModel" style="height: 100vh">
    <template v-slot:before>
      <div class="q-mx-lg">

<div class="q-mb-md">
  <q-btn @click="showDialog = true" no-caps icon="restart_alt" color="text-white" glossy label="Convert from Markdown" />
</div>

        <q-input
          placeholder="Insert JSON here"
          debounce="500"
          v-model="jsonInput"
          rows="30"
          input-class="text-white"
          autofocus
          outlined
          type="textarea"
        />
        <q-sticky position="bottom-left">
          <q-badge
            v-if="hasParseError"
            color="red"
            label="The JSON input is invalid"
          />
        </q-sticky>
      </div>
    </template>

    <template v-slot:after>
      <div class="q-mx-lg text-white">
        <div class="q-mb-lg text-h6">JSON As Object</div>
        {{ parsedJsonObject }}
        <div class="q-my-md">
          <q-btn @click="copyAsText()" dark color="primary" no-caps label="Copy JSON Object as Text" />
        </div>

        <div class="q-mt-xl q-mb-lg text-h6">JSON As Database</div>
       <Table v-if="parsedJsonObject" 
       @add="onTableAdd()"
       @update="onTableUpdate($event)"
       @delete="onTableDelete($event)" :jsonObject="parsedJsonObject" />  
      </div>
    </template>
  </q-splitter>
  </div>

</template>
<script>
import { copyToClipboard } from 'quasar'
import Table from 'components/Table.vue'
import DialogConvert from 'components/DialogConvert.vue'
export default {
  components: {
    Table,
    DialogConvert
  },
  watch: {
    jsonInput(val) {
      try {
        this.hasParseError = false;
        this.parsedJsonObject = JSON.parse(val);
        this.$q.notify("Updating JSON Object Model");
      } catch (e) {
        this.$q.notify({
          type: "negative",
          message: `There was an error parsing the JSON input`,
        });
        this.hasParseError = true;
      }
    },
  },
  mounted() {
    try {
      this.hasParseError = false;
      this.parsedJsonObject = JSON.parse(this.jsonInput);
    } catch (e) {
      this.$q.notify({
        type: "negative",
        message: `There was an error parsing the JSON input`,
      });
      this.hasParseError = true;
    }
  },
  data() {
    return {
      showDialog: false,
      hasParseError: false,
      jsonInput: `
[
  "Item 1","Item 2"
]
      `,
      parsedJsonObject: null,
      splitterModel: 50, // start at 50%
    };
  },
  methods: {
    onConvert(arr) {
      this.parsedJsonObject = arr
      this.jsonInput = JSON.stringify(arr, null, 2)
    },
    onTableDelete(index) {
      this.parsedJsonObject.splice(index, 1)
    },
    onTableAdd() {
      this.parsedJsonObject.push('New row')
    },
    onTableUpdate(payload) {
      this.parsedJsonObject[payload.index] = payload.val
    },
    copyAsText() {
      let str = JSON.stringify(this.parsedJsonObject, null, 2)
      copyToClipboard(str)
      .then(() => {
        this.$q.notify('JSON copied to clipboard')
      })
      .catch(() => {
      // fail
      })
    }
  }
};
</script>