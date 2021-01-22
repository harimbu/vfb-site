<template>
  <div>
    <v-list-item>
      <v-list-item-content>
        <v-list-item-title class="title">
          Application
        </v-list-item-title>
        <v-list-item-subtitle>
          subtext
        </v-list-item-subtitle>
      </v-list-item-content>
      <v-list-item-action>
        <v-btn icon @click="mode"><v-icon v-text="$store.state.edit ? 'mdi-eye' : 'mdi-cog'"></v-icon></v-btn>
      </v-list-item-action>
    </v-list-item>

    <v-divider></v-divider>

    <v-list>
      <v-list-group v-for="(item, i) in items" :key="i" v-model="item.active" :prepend-icon="item.icon" no-action>
        <template v-slot:activator>
          <v-list-item-content>
            <v-list-item-title>
              {{ item.title }}
              <span v-if="$store.state.edit">
                <v-btn icon @click="openDialog(i)"><v-icon>mdi-pencil</v-icon></v-btn>
                <v-btn icon @click="moveItem(items, i, -1)" v-if="i > 0"><v-icon>mdi-chevron-double-up</v-icon></v-btn>
                <v-btn icon @click="moveItem(items, i, 1)" v-if="i < items.length - 1"><v-icon>mdi-chevron-double-down</v-icon></v-btn>
                <v-btn icon @click="deleteItem(i)"><v-icon>mdi-delete</v-icon></v-btn>
              </span>
            </v-list-item-title>
          </v-list-item-content>
        </template>

        <v-list-item v-for="(child, j) in item.items" :key="j" :to="$store.state.edit ? '' : child.to">
          <v-list-item-content>
            <v-list-item-title>
              {{ child.title }}
              <span v-if="$store.state.edit">
                <v-btn icon @click="openDialogSub(i, j)"><v-icon>mdi-pencil-box</v-icon></v-btn>
                <v-btn icon @click="moveItem(item.items, j, -1)" v-if="j > 0"><v-icon>mdi-chevron-double-up</v-icon></v-btn>
                <v-btn icon @click="moveItem(item.items, j, 1)" v-if="j < item.items.length - 1"><v-icon>mdi-chevron-double-down</v-icon></v-btn>
                <v-btn icon @click="deleteSubItem(i, j)"><v-icon>mdi-delete</v-icon></v-btn>
              </span>
            </v-list-item-title>
          </v-list-item-content>
        </v-list-item>

        <!-- 서브 메뉴 추가 -->
        <v-list-item v-if="$store.state.edit" @click="openDialogSub(i, -1)">
          <v-list-item-icon><v-icon>mdi-plus</v-icon></v-list-item-icon>
          <v-list-item-content>
            <v-list-item-title>서브 추가하기</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
        <!--// 서브 메뉴 추가 -->
      </v-list-group>

      <!-- 메뉴 추가 -->
      <v-list-item v-if="$store.state.edit" @click="openDialog(-1)">
        <v-list-item-icon><v-icon>mdi-plus</v-icon></v-list-item-icon>
        <v-list-item-content>
          <v-list-item-title>추가하기</v-list-item-title>
        </v-list-item-content>
      </v-list-item>
      <!--// 메뉴 추가 -->
    </v-list>

    <!-- dialog -->
    <v-dialog v-model="dialog" max-width="400">
      <v-card>
        <v-card-title>
          <span>{{ selectIndex === -1 ? '메뉴추가' : '메뉴수정' }}</span>
          <v-spacer></v-spacer>
          <v-btn color="success" icon @click="saveItem"><v-icon>mdi-content-save</v-icon></v-btn>
        </v-card-title>
        <v-card-text>
          <v-row>
            <v-col cols="12" sm="4">
              <v-text-field v-model="formItem.icon" label="icon"></v-text-field>
            </v-col>
            <v-col cols="12" sm="8">
              <v-text-field v-model="formItem.title" label="menu"></v-text-field>
            </v-col>
          </v-row>
        </v-card-text>
      </v-card>
    </v-dialog>
    <!--// dialog -->

    <!-- sub dialog -->
    <v-dialog v-model="dialogSub" max-width="400">
      <v-card>
        <v-card-title>
          <span>{{ selectSubIndex === -1 ? '추가 : 서브메뉴' : '수정 : 서브메뉴' }}</span>
          <v-spacer></v-spacer>
          <v-btn color="success" icon @click="saveItemSub"><v-icon>mdi-content-save</v-icon></v-btn>
        </v-card-title>
        <v-card-text>
          <v-row>
            <v-col cols="12" sm="4">
              <v-text-field v-model="formItemSub.to" label="to"></v-text-field>
            </v-col>
            <v-col cols="12" sm="8">
              <v-text-field v-model="formItemSub.title" label="menu"></v-text-field>
            </v-col>
          </v-row>
        </v-card-text>
      </v-card>
    </v-dialog>
    <!--// sub dialog -->
  </div>
</template>
<script>
export default {
  props: ['items'],
  data() {
    return {
      dialog: false,
      dialogSub: false,
      selectIndex: null,
      selectSubIndex: null,
      formItem: {
        title: '',
        icon: ''
      },
      formItemSub: {
        title: '',
        to: ''
      }
    }
  },
  methods: {
    mode() {
      this.$store.commit('setMode', !this.$store.state.edit)
    },
    save() {
      this.$firebase
        .database()
        .ref()
        .child('site')
        .child('items')
        .set(this.items)
      this.dialog = false
      this.dialogSub = false
    },
    openDialog(index) {
      this.selectIndex = index
      this.dialog = true

      if (this.selectIndex < 0) {
        this.formItem.title = ''
        this.formItem.icon = ''
      } else {
        this.formItem.title = this.items[this.selectIndex].title
        this.formItem.icon = this.items[this.selectIndex].icon
      }
    },
    saveItem() {
      if (this.selectIndex < 0) {
        this.items.push(this.formItem)
      } else {
        this.items[this.selectIndex].title = this.formItem.title
        this.items[this.selectIndex].icon = this.formItem.icon
      }
      this.save()
    },

    openDialogSub(index, subIndex) {
      this.selectIndex = index
      this.selectSubIndex = subIndex
      this.dialogSub = true

      if (this.selectSubIndex < 0) {
        this.formItemSub.title = ''
        this.formItemSub.to = ''
      } else {
        this.formItemSub.title = this.items[this.selectIndex].items[this.selectSubIndex].title
        this.formItemSub.to = this.items[this.selectIndex].items[this.selectSubIndex].to
      }
    },
    saveItemSub() {
      if (this.selectSubIndex < 0) {
        if (!this.items[this.selectIndex].items) this.items[this.selectIndex].items = []
        this.items[this.selectIndex].items.push(this.formItemSub)
      } else {
        this.items[this.selectIndex].items[this.selectSubIndex].title = this.formItemSub.title
        this.items[this.selectIndex].items[this.selectSubIndex].to = this.formItemSub.to
      }
      this.save()
    },
    moveItem(items, i, arrow) {
      // const item = items.splice(i, 1)[0]
      // items.splice(i + arrow, 0, item)
      items.splice(i + arrow, 0, ...items.splice(i, 1))
      this.save()
    },
    deleteItem(i) {
      this.items.splice(i, 1)
      this.save()
    },
    deleteSubItem(i, j) {
      this.items[i].items.splice(j, 1)
      this.save()
    }
  }
}
</script>
