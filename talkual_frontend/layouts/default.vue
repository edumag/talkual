<template>
  <div>
    <nav class="navbar header has-shadow is-primary" role="navigation" aria-label="main navigation">
      <div class="navbar-brand">
        <a class="navbar-item" href="/"><img src="/logo.png"/></a>
        <div class="navbar-burger" @click="isActive = !isActive">
          <span/>
          <span/>
          <span/>
        </div>
      </div>

      <!-- a partir de aqui el contenido -->
      <div class="navbar-menu" :class="{ active: isActive }">
        <div class="navbar-end">
          <div class="navbar-item">
            <div class="buttons">
              <a class="button" v-if="!user" @click="isRegisterActive=true">
                <strong>Registro</strong>
              </a>
              <a class="button is-primary" v-else>
                Wellcome {{user}}
              </a>
              <a class="button is-light" @click="isLoginActive=true" v-if="!user">
                Acceder
              </a>
              <a class="button is-light" @click="logout" v-else>
                Salir
              </a>
              <!-- Modal para el login -->
              <b-modal :active.sync="isLoginActive">
                <form action="" v-on:submit.prevent="login">
                  <div class="modal-card" style="width: auto">
                    <header class="modal-card-head">
                      <p class="modal-card-title">Identificación</p>
                    </header>
                    <section class="modal-card-body">
                      <b-field label="Email">
                        <b-input
                          type="email"
                          v-model="loginEmail"
                          placeholder="Tu email"
                          required>
                        </b-input>
                      </b-field>
                      <b-field label="Contraseña">
                        <b-input
                          type="password"  
                          v-model="loginPassword"
                          password-reveal
                          placeholder="Tu contraseña"
                          required>
                        </b-input>
                      </b-field>
                  </section>
                  <footer class="modal-card-foot">
                    <button class="button is-primary">Acceder</button>
                  </footer>
                </div>
              </form>
              </b-modal>
              <!-- Modal para el registro -->
              <b-modal :active.sync="isRegisterActive">
                <form action="" v-on:submit.prevent="register">
                  <div class="modal-card" style="width: auto">
                    <header class="modal-card-head">
                      <p class="modal-card-title">Registro</p>
                    </header>
                    <section class="modal-card-body">
                      <b-field label="Email">
                        <b-input
                          type="email"
                          v-model="registerEmail"
                          placeholder="Tu email"
                          required>
                        </b-input>
                      </b-field>
                      <b-field label="Contraseña">
                        <b-input
                          type="password"  
                          v-model="registerPassword1"
                          password-reveal
                          placeholder="Tu contraseña"
                          required>
                        </b-input>
                      </b-field>
                      <b-field label="Repite la contraseña">
                        <b-input
                          type="password"  
                          v-model="registerPassword2"
                          password-reveal
                          placeholder="Tu contraseña"
                          required>
                        </b-input>
                      </b-field>
                  </section>
                  <footer class="modal-card-foot">
                    <button class="button is-primary">Registrarse</button>
                  </footer>
                </div>
              </form>
              </b-modal>
            </div>
          </div>
        </div>
      </div>
    </nav>

      <div class="container column is-9">
        <nuxt/>
      </div>
    </section>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  computed: {
    user: function () {
      return this.$store.state.user;
    }
  },
  data() {
    return {
      isActive: false,
      isLoginActive: false,
      isRegisterActive: false,

      loginEmail: 'test@lesolivex.com',
      loginPassword: 'Password2023',
      
      registerEmail: 'test2@lesolivex.com',
      registerPassword1: 'Password2023',
      registerPassword2: 'Password2023',

    };
  },
  methods: {
    login() {
      return this.$axios.post('/auth/local/', {
        identifier: this.loginEmail,
        password: this.loginPassword
      })
      .then((res) => {
        if (res.data.jwt){
          this.$store.commit('saveToken', res.data.jwt)
          this.$store.commit('saveUser', this.loginEmail.split("@")[0])
          // Reiniciamos los campos
          this.loginEmail = ''
          this.loginPassword = ''
          // Escondemos la modal
          this.isLoginActive = false;
          this.$router.replace({ path: '/products' })
        }
      })
      .catch((error) => {
        //alert(Object.values(error.response.data))
        this.$toasted.error(Object.values(error.response.data))
      })
    },
    register() {
      return this.$axios.post('/auth/registration/', {
        email: this.registerEmail,
        password1: this.registerPassword1,
        password2: this.registerPassword2
      })
      .then((res) => {
        if (res.data.key){
          this.$store.commit('saveToken', res.data.key)
          this.$store.commit('saveUser', this.registerEmail.split("@")[0])
          this.registerEmail = ''
          this.registerPassword1 = ''
          this.registerPassword2 = ''
          this.isRegisterActive = false;
        }
      })
      .catch((error) => {
        this.$toasted.error(Object.values(error.response.data))
      })
    },
    logout() {
      this.$store.commit('saveToken', null)
      this.$store.commit('saveUser', null)
      this.$router.replace({ path: '/' })
    }
  }
};
</script>

<style scoped>
  .active{
    display: block !important;
  }
</style>

