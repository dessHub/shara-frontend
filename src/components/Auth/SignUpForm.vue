<template>
  <div class="ui stackable three column centered grid container">
    <div class="column">
      <h2 class="ui dividing header">Sign Up, it's free!</h2>

      <Notification
        :message="notification.message"
        :type="notification.type"
        v-if="notification.message"
      />

     <ValidationObserver v-slot="{ invalid }">
        <form class="ui form" @submit.prevent="signup">
          <div class="field" >
            <label>Full Name</label>
            <ValidationProvider rules="required" v-slot="v">
              <input type="text" name="name" :class="{'input': true, 'is-danger':
                v.errors[0] }" v-model="name" v-validate="'required'" placeholder="Full name">
              <span class="is-danger">{{ v.errors[0] }}</span>
            </ValidationProvider>
          </div>

          <div class="field" >
            <label>Phone Number</label>
            <ValidationProvider rules="required" v-slot="v">
              <input type="text" name="phone" :class="{'input': true, 'is-danger':
                v.errors[0] }" v-model="phone" v-validate="'required'"
                placeholder="Phone No +2547...">
                <span class="is-danger">{{ v.errors[0] }}</span>
              </ValidationProvider>
            </div>

            <div class="field" >
              <label>Email</label>
              <ValidationProvider rules="required|email" v-slot="v">
                <input type="email" name="email" :class="{'input': true, 'is-danger': v.errors[0] }" v-model="email" v-validate="'required|email'" placeholder="Email">
                <span class="is-danger">{{ v.errors[0] }}</span>
              </ValidationProvider>
            </div>

            <div class="field">
              <label>Password</label>
              <ValidationProvider rules="required" v-slot="v">
                <input type="password" name="password" :class="{'input': true,
                  'is-danger': v.errors[0] }" v-model="password" v-validate="'required'" placeholder="Password">
                <span class="is-danger">{{ v.errors[0] }}</span>
              </ValidationProvider>
            </div>

            <button class="fluid ui primary button" :disabled="invalid">SIGN UP</button>

            <div class="ui hidden divider"></div>
          </form>
        </ValidationObserver>

        <div class="ui divider"></div>

        <div class="ui column grid">
          <div class="center aligned column">
            <p>
              Got an account? <router-link to="/login">Log In</router-link>
            </p>
          </div>
        </div>
      </div>
    </div>
  </template>

<script>
import Notification from '@/components/Notification'

export default {
  name: 'SignUpForm',
  components: {
    Notification
  },

  data () {
    return {
      name: '',
      phone: '',
      email: '',
      password: '',
      notification: {
        message: '',
        type: ''
      }
    }
  },
  computed: {},
  beforeRouteEnter  (to, from, next) {
    const token = localStorage.getItem('auth-token');

    return token ? next('/') : next();
  },
  methods: {
    signup() {
      axios
      .post('/register', {
        name: this.name,
        phone: this.phone,
        email: this.email,
        password: this.password,
      })
      .then(response => {
        // save token in localstorage
        console.log("data", response.data)
        localStorage.setItem('auth-token', response.data.token);

        // redirect to user home
        this.$router.push('/');
      })
      .catch(error => {
        // display error notification
        console.log("error", error)
        this.notification = Object.assign({}, this.notification, {
          message: error.response.data.message,
          type: error.response.data.status,
        });
      });
    },
  },
};
</script>
