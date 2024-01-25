<!-- 

  
  Student instructions to create this component:

  The functionality of this component is two fold: 
  1. Display a link that toggles between "Go to login", "Go to register", and "Logout" depending on the value of the isLoggedIn prop: By default, it is "Go to register", when the user is not logged in.  
  - User logged in: display "Logout". The link should emit a logout event when clicked.
  - User not logged in and in login: display "Go to register". 
  - User not logged in and in register: display "Go to login".
  
  2. When user is trying to log in or register, the component should display a form with two input fields and a submit button.  The form should have an id of "auth-form". The form should submit the username and password to the submit function when submitted. The input fields should be required.

  - One input field for username with an id of "username", name of "auth-username" and type of "text".
  - One input field for password with an id of "password", name of "auth-password" and type of "password".
  - A submit button with a class of "btn-auth" with the text "login" or "register" depending on the current state of the component. If the user is trying to login, the button should say "login" and emit a "login" event with the username and password. If the user is trying to register, the button should say "register" and emit a "register" event with the username and password.

  Once user is logged in or registered, the form should be hidden and the link should change to "Logout".

 -->

 <template>
  <div>
    <a @click.prevent="toggleState">{{ linkText }}</a>
    <form v-if="!isLoggedIn" @submit.prevent="submit" id="auth-form">
      <input id="username" v-model="username" name="auth-username" type="text" required />
      <input id="password" v-model="password" name="auth-password" type="password" required />
      <button type="submit" class="btn-auth">{{ buttonText }}</button>
    </form>
  </div>
</template>

 <script>

 export default {
  props: {
    isLoggedIn: {
      type: Boolean,
      default: false
    }
  },

  data() {
    return {
      isLogin: true,
      username: '',
      password: '',
    }
  },
  computed: {
    linkText() {
      if (this.isLoggedIn) {
        return 'Logout';
      }
      else{
        return this.isLogin ? 'Go to register' : 'Go to login';
      }
    },
    buttonText() {
      return this.isLogin ? 'login' : 'register';
    }
  },

  methods: {
    toggleState() {
      if(this.isLoggedIn){
        this.$emit('logout');
      }
      else{
        this.isLogin = !this.isLogin;
      }
    },

    submit() {
      this.$emit(this.isLogin ? 'login' : 'register', {username: this.username, password: this.password});
      this.username = ''
      this.password = ''
    }
  }
 }
</script>