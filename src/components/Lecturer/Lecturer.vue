<template>
    <v-container fluid fill-height id="background">
      <v-toolbar app>
        <v-toolbar-title class="title_app" >
          <strong>{{ $store.state.route.name }}</strong></v-toolbar-title>
        <v-spacer></v-spacer>
        <v-btn icon :ripple="false" v-if="responsive" to="/lecturer/dashboard">
          <v-tooltip bottom>
            <v-icon slot="activator" color="#43425D">dashboard</v-icon>
            <span class="text-tooltip">Dashboard</span>
          </v-tooltip>
        </v-btn>
        <v-btn icon :ripple="false" v-if="responsive" to="/lecturer/profile">
          <v-tooltip bottom>
            <v-icon slot="activator" color="#43425D">person</v-icon>
            <span class="text-tooltip">User Profile</span>
          </v-tooltip>
        </v-btn>
        <v-btn icon :ripple="false" v-if="responsive" @click="onLogout">
          <v-tooltip bottom>
            <v-icon slot="activator" color="#43425D">exit_to_app</v-icon>
            <span class="text-tooltip">Logout</span>
          </v-tooltip>
        </v-btn>
      </v-toolbar>
      <appDrawer />
      <router-view></router-view>
      <app-footer/>
    </v-container>
</template>

<script>
import CourseOverview from './CourseOverview.vue'
import Drawer from './Drawer.vue'
import Footer from '../UI/Footer.vue'
import { mapMutations } from 'vuex'

export default {
  data () {
    return {
      responsive: false
    }
  },
  components: {
    courseOverview: CourseOverview,
    appDrawer: Drawer,
    appFooter: Footer
  },
  computed: {
    isLoggedIn () {
      return this.$store.getters.isLoggedIn
    }
  },
  created () {
    this.$store.dispatch('getUser')
    this.$store.dispatch('lecturer/getCourses')
  },
  mounted () {
    this.onResponsiveInverted()
    window.addEventListener('resize', this.onResponsiveInverted)
  },
  beforeDestroy () {
    window.removeEventListener('resize', this.onResponsiveInverted)
  },
  methods: {
    ...mapMutations('app', ['setDrawer', 'toggleDrawer']),
    onResponsiveInverted () {
      if (window.innerWidth < 991) {
        this.responsive = true
      } else {
        this.responsive = false
      }
    },
    onLogout () {
      this.$store.dispatch('logout')
        .then(() => {
          this.$router.push('/login')
        })
    }
  }
}
</script>

<style scoped>
#background {
  background-color: #DBDBDB;
},
.title_app {
  color:  #43425D;
  font-size: 25px;
}
.text-tooltip {
  color:  #FFF;
  font-size: 14px;
}
</style>
