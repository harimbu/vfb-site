<template>
  <v-col class="text-center" cols="12">
    {{ new Date().getFullYear() }} —
    <strong>
      {{ footer }}
      <v-btn icon v-if="$store.state.edit" @click="openDialog"><v-icon>mdi-pencil</v-icon></v-btn>
    </strong>

    <v-dialog v-model="dialog" max-width="500">
      <v-card>
        <v-card-title>
          풋터 수정
          <v-spacer></v-spacer>
          <v-btn color="success" icon @click="save"><v-icon>mdi-content-save</v-icon></v-btn>
        </v-card-title>
        <v-card-text>
          <v-text-field v-model="text"></v-text-field>
        </v-card-text>
      </v-card>
    </v-dialog>
  </v-col>
</template>
<script>
export default {
  props: ['footer'],
  data() {
    return {
      dialog: false,
      text: this.footer
    }
  },
  methods: {
    openDialog() {
      this.dialog = true
      this.text = this.footer
    },
    save() {
      this.$firebase
        .database()
        .ref()
        .child('site')
        .update({ footer: this.text })

      this.dialog = false
    }
  }
}
</script>
