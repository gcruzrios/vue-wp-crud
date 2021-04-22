<template>
  <v-container>
    <v-row class="text-center">
      <v-col cols=4></v-col>
      <v-col cols="4">
        <v-img
          :src="require('../assets/logo.svg')"
          class="my-3"
          contain
          height="200"
        />
        <h1 class="display-2 font-weight-bold mb-10">
          Welcome to WP CRUD
        </h1>

        <form>
          <v-text-field
            v-model="username"
            :error-messages="usernameErrors"
            :counter="20"
            label="Username"
            required
            @input="$v.username.$touch()"
            @blur="$v.username.$touch()"
            
          ></v-text-field>
          <v-text-field
            v-model="password"
            :error-messages="passwordErrors"
            :counter="20"
            label="Password"
            type="password"
            required
            @input="$v.password.$touch()"
            @blur="$v.password.$touch()"
            
          ></v-text-field>
          

          <v-btn
            class="mr-4"
            @click="submit"
          >
          <!--  -->
 
            submit
          </v-btn>
          <v-btn @click="clear">
            clear
          </v-btn>
        </form>
      </v-col>

      <v-col cols="4" class="lg-3 mb-4">
        
        
        
      </v-col>
      
      

     
    </v-row>
  </v-container>
</template>

<script>

  import axios from 'axios';
  import { validationMixin } from 'vuelidate'
  import { required, maxLength } from 'vuelidate/lib/validators'

  export default {
    
    name: 'HelloWorld',
    mixins: [validationMixin],

    validations: {
      username: { required, maxLength: maxLength(20) },
      password: { required, maxLength: maxLength(20) },
    },

    data: () => ({
      username: '',
      password: '',
    
    }),
     computed: {
      
      usernameErrors () {
        const errors = []
        if (!this.$v.username.$dirty) return errors
        !this.$v.username.maxLength && errors.push('Username must be at most 20 characters long')
        !this.$v.username.required && errors.push('Username is required.')
        return errors
      },
      passwordErrors () {
        const errors = []
        if (!this.$v.password.$dirty) return errors
     
        !this.$v.password.required && errors.push('Password is required')
        return errors
      },
    },

    methods: {
      submit () {

        this.error=null;
         
        //console.log(this.username)
        //console.log(this.password)
        axios.post('jwt-auth/v1/token/', {username: this.username, password: this.password})
        
        .then(respuesta => {
            //console.log(respuesta.data);
            return respuesta.data

        }).then(data => {        
           this.$store.dispatch("guardarToken", data.token)
           this.$router.push({ name: 'Crud' })
        }).catch(err => {

          console.log(err);
          alert("Credenciales inválidas");
        
        })

       // this.$v.$touch()
      },
      clear () {
        this.$v.$reset()
        this.username = ''
        this.password = ''
        
      },
    },
  }
</script>
