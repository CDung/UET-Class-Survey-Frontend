<template>
  <v-layout id="layout-user-profile" align-center justify-center row fill-height>
    <v-flex xs12 sm11 md10 lg7 xl4>
      <v-card id="card-user-profile">
        <v-tooltip top @click.native="$refs.changeLecturerAvatar.click()">
          <v-avatar
            style="cursor: pointer; justify-self: center;"
            slot="activator"
            class="mx-auto d-block"
            size="342"
          >
            <img :src="avatar"/>
          </v-avatar>
          <span>Click to change Avatar</span>
          <input hidden type="file" multiple="false" accept=".jpg, .png" ref="changeLecturerAvatar" @change="changeAvatar">
        </v-tooltip>
        <v-card-text class="text-xs-center">
          <div id="user-role"><em>{{ role }}</em></div>
          <div id="user-name"><strong>{{ user.fullname }}</strong></div>
          <div id="user-grade">{{ user.classname }}</div>
          <div id="user-email">{{ user.vnuemail }}</div>
          <div id="user-courses"><strong>Total courses: {{ totalCourses }}</strong></div>
        </v-card-text>
        <v-card-text class="text-xs-center">
          <v-btn v-if="!showAvatar" color="mainColor" dark @click="editInformation">Edit Information</v-btn>
          <v-btn v-else color="mainColor" dark @click="submitAvatar()">Save</v-btn>
        </v-card-text>
      <v-snackbar
        top
        v-model="snackbar.value"
        :color="snackbar.colorSnackbar"
        :timeout="snackbar.snackbarTimeout"
      >
        <v-icon>check_circle</v-icon>
        <v-btn color="#66615B" flat @click="snackbar.value = false">{{ snackbar.snackbarMessage }}</v-btn>
      </v-snackbar>
      <v-dialog v-model="dialog" max-width="600px">
        <edit-information
          :key="key"
          @closeDialog='dialog=$event'
          @snackbarMessage='snackbar.snackbarMessage=$event'
          @showSnackbar='snackbar.value=$event'
          :userInfo="user">
        </edit-information>
      </v-dialog>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
import { mapState } from 'vuex'
import EditInformation from './EditInformation'

export default {
  data () {
    return {
      file: '',
      showAvatar: false,
      snackbar: {
        value: false,
        snackbarMessage: 'Update Avatar successfully',
        snackbarTimeout: 3000,
        colorSnackbar: 'white'
      },
      role: localStorage.getItem('roleStatus'),
      dialog: false,
      key: 0
    }
  },
  computed: {
    ...mapState({
      user: state => state.user,
      avatar: state => state.avatar,
      totalCourses: state => state.lecturer.totalCourses
    })
  },
  methods: {
    submitAvatar () {
      let formData = new FormData()
      formData.append('file', this.file)
      this.$store.dispatch('lecturer/changeAvatar', formData)
        .then(() => {
          this.showAvatar = false
          this.showSnackbar()
          this.$refs.changeStudentAvatar.value = ''
        })
        .catch((err) => {
          console.log(err)
        })
    },
    changeAvatar () {
      this.file = this.$refs.changeLecturerAvatar.files[0]
      let reader = new FileReader()
      reader.addEventListener('load', function () {
        this.showAvatar = true
        this.$store.commit('SET_TEMP_AVATAR', reader.result)
      }.bind(this), false)
      if (this.file) {
        if (/\.(jpe?g|png)$/i.test(this.file.name)) {
          reader.readAsDataURL(this.file)
        }
      }
    },
    showSnackbar () {
      this.snackbar.value = true
    },
    editInformation () {
      this.dialog = true
      this.key++
    }
  },
  components: {
    editInformation: EditInformation
  }
}
</script>

<style scoped>
#layout-user-profile {
  margin: 20px;
}
#card-user-profile {
  padding-top: 50px;
  padding-bottom: 20px;
  padding-left: 60px;
  padding-right: 60px;
}
#user-role {
  margin: 12px;
  font-weight: semibold;
  color: #43425D;
  font-size: 30px;
}
#user-name {
  margin: 12px;
  font-weight: black;
  color: #43425D;
  font-size: 30px;
}
#user-grade {
  margin: 12px;
  font-weight: bold;
  color: #43425D;
  font-size: 25px;
}
#user-email {
  margin: 12px;
  color: #43425D;
  font-size: 25px;
}
#user-courses {
  margin: 12px;
  font-weight: semibold;
  color: #43425D;
  font-size: 25px;
}
</style>
