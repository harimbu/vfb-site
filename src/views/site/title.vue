<template>
  <v-toolbar-title>
    {{ title }}
    <v-btn icon v-if="$store.state.edit" @click="openDialog"><v-icon>mdi-pencil</v-icon></v-btn>

    <v-dialog v-model="dialog" max-width="500">
      <v-card>
        <v-card-title>
          타이틀 수정
          <v-spacer></v-spacer>
          <v-btn color="success" icon @click="save"><v-icon>mdi-content-save</v-icon></v-btn>
        </v-card-title>
        <v-card-text>
          <v-text-field v-model="text"></v-text-field>
        </v-card-text>
      </v-card>
    </v-dialog>
  </v-toolbar-title>
</template>
<script>
export default {
  props: ['title'],
  data() {
    return {
      dialog: false,
      text: this.title
    }
  },
  methods: {
    openDialog() {
      this.dialog = true
      this.text = this.title
    },
    save() {
      this.$firebase
        .database()
        .ref()
        .child('site')
        .update({ title: this.text })

      this.dialog = false
    }
  }
}
</script>
