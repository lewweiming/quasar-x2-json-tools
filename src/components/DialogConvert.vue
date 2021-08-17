<!--
- Follows the guide from https://randyperkins2k.medium.com/writing-a-simple-markdown-parser-using-javascript-1f2e9449a558
-->
<template>
<q-dialog v-model="showModal">
  <q-card bordered dark style="min-width: 500px" class="q-pa-md">
    <q-card-section>
      <div class="text-h6">Convert from Markdown</div>
      <p>Converts a simple markdown list into JSON text</p>
      <q-item>
          <q-item-section avatar>
              <q-avatar>
                  <q-icon name="warning" />
                  </q-avatar>
              </q-item-section>
              <q-item-section>
                  This feature is experimental and may not work accordingly.
              </q-item-section>
    </q-item>

    </q-card-section>

    <q-card-section class="q-pt-none">
	<q-input v-model="inputMarkdown" dark placeholder="Insert markdown text here" type="textarea" outlined autofocus />
    </q-card-section>

    <q-card-actions align="right">
        <div class="q-gutter-x-md">
      <q-btn flat label="Cancel" v-close-popup />
      <q-btn @click="convert()" color="primary" label="Convert" v-close-popup />
      </div>
    </q-card-actions>
  </q-card>
</q-dialog>
</template>

<script>
export default {
  props: {
    show: Boolean,
    colorScheme: String
  },
  computed: {
    showModal: {
      get: function() {
        return this.show;
      },
      set: function(val) {
        if (!val) {
          this.$emit('close')
        }
      }
    }
  },
  data() {
    return {
inputMarkdown: `
- item 1
- item 2
- item 3
`
    }
  },
  methods: {
    convert() {
        let result = this.parseMarkdownListToArray(this.inputMarkdown)
        this.$emit('convert', result)
    },
    /* Returns a JS Array */
    parseMarkdownListToArray(text) {
        let regex = /^- (.*$)/gim;
        let result;
        let arr = []; // The final array to be returned
        while((result = regex.exec(text)) !== null) {
            arr.push(result[1])
        }

        return arr
    }
  }
}
</script>
