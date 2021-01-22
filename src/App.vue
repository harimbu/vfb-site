<template>
  <v-app>
    <v-navigation-drawer v-model="drawer" app width="400">
      <Menu :items="site.items" />
    </v-navigation-drawer>

    <v-app-bar app flat>
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      <Title :title="site.title" />
      <v-spacer></v-spacer>
      <v-btn icon><v-icon>mdi-magnify</v-icon></v-btn>
      <v-btn icon><v-icon>mdi-heart</v-icon></v-btn>
    </v-app-bar>

    <v-main>
      <v-container fluid>
        <router-view></router-view>
      </v-container>
    </v-main>

    <v-footer padless>
      <Footer :footer="site.footer" />
    </v-footer>
  </v-app>
</template>

<script>
import Menu from '@/views/site/menu'
import Title from '@/views/site/title'
import Footer from '@/views/site/footer'
export default {
  components: { Menu, Title, Footer },
  data() {
    return {
      drawer: null,
      site: {
        title: 'Vue.js',
        footer: 'vuetify',
        items: [
          {
            icon: 'mdi-home',
            items: [{ title: '홈', to: '/' }],
            title: '대시보드',
            active: true
          },
          {
            icon: 'mdi-git',
            items: [
              { title: '리엑트', to: '/react' },
              { title: '뷰', to: '/vue' },
              { title: '앵귤러', to: '/angular' }
            ],
            title: '자바스크립트'
          },
          {
            icon: 'mdi-github',
            items: [
              { title: '공지사항', to: '/notice' },
              { title: '갤러리', to: '/gallery' }
            ],
            title: '게시판'
          }
        ]
      }
    }
  },
  created() {
    this.subscribe()
  },
  methods: {
    subscribe() {
      const db = this.$firebase
        .database()
        .ref()
        .child('site')

      db.on('value', sn => {
        const v = sn.val()
        if (!v) {
          db.set(this.site)
          return
        } else {
          this.site = v
        }
      })
    }
  }
}
</script>
