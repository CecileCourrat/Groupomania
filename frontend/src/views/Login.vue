<template>
 <div>
  <header class="logo__image">
     <img src="../assets/logo.png" alt="Logo Groupomania">
  </header>
   <div class="authentification">
    <h1 v-if="mode == 'login' ">Connexion</h1>
    <h1 v-else>Inscription</h1>
     <div class="form" v-if="mode == 'create'">
      <input v-model="prenom" type="text" placeholder="Prénom">
      <input v-model="nom" type="text" placeholder="Nom">
     </div>
     <div class="form">
      <input v-model="email" type="email" placeholder="Adresse email">
      <input v-model="password" type="password" placeholder="Mot de passe">
       <p class="error__msg">{{ error }}</p>
      <button @click="login()" v-if="mode == 'login'">Connexion</button>
      <button @click="createAccount()" v-else>Inscription</button>
      <p v-if="mode == 'login' ">Vous n'avez pas encore de compte ? <span class="login" @click="switchToCreateAccount()">Créer un compte</span></p>
      <p v-else>Vous avez déjà un compte ? <span class="login" @click="switchToLogin()">Se connecter</span></p>
     </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'login',
  data () {
       return {
       mode: 'login',
       prenom: '',
       nom: '',
       email: '',
       password: '',
       error: '',
       userRegex: /^[-'a-zA-ZÀ-ÖØ-öø-ÿ\s]{3,}$/,
       emailRegex: /^[a-zA-Z0-9.-_]+[@]{1}[a-zA-Z0-9.-_]+[.]{1}[a-z]{2,10}$/,
       passwordRegex:  /^(?=.*[0-9])(?=.*[a-z])(?=.*[A-Z]).{6,50}$/
      };
},

methods: {   
    switchToCreateAccount() {
      this.mode = 'create';
    },

    switchToLogin() {
      this.mode = 'login';
    }, 

    createAccount() {
      if (!this.userRegex.test(this.prenom)) {
        return (this.error = 'Prénom non valide');
        } else if (!this.userRegex.test(this.nom)) {
        return (this.error = 'Nom non valide');
        } else if (!this.emailRegex.test(this.email)) {
        return (this.error = 'Email non valide');
        } else if (!this.passwordRegex.test(this.password)) {
        return (this.error = 'Votre mot de passe doit contenir au moins 6 caractères, un chiffre, une minuscule et une majuscule');
        }
      const self = this;
      axios.post('http://localhost:3000/api/user/signup', {
        prenom: this.prenom,
        nom: this.nom,
        email: this.email,
        password: this.password
      })
      .then(() => {
        self.login();
      })
      .catch(error => {
        console.log(error);
      });
    },
    
    login () {
      const self = this;
      axios.post('http://localhost:3000/api/user/login', {
        email: this.email,
        password: this.password,
      })
      .then(response => {
        localStorage.setItem('userId', response.data.userId);
        localStorage.setItem('token', response.data.token);
        self.$router.push('/post')
        })
        .catch(error => {
        console.log(error);
      });
    },
  }
} 
</script>


<style scoped lang="scss">
.logo__image {
    text-align: center;
    img {
      width: 200px; 
    }
}

.authentification {
  box-shadow: -1px 6px 18px 8px #d4d4d4;
  border-radius: 20px;
  width: 400px;
  @media screen and (max-width: 500px) {
    width: 250px;
  }
  display: flex;
  flex-direction: column;
  margin: auto;
  align-items: center;
}

.form {
    display: flex;
    flex-direction: column;
    width: 80%;
    input {
        margin: 10px;
        height: 30px;
        padding: 5px;
    }
    button {
        border-radius: 20px;
        height: 50px;
        border: none;
        font-weight: bold;
        font-size: 16px;
        box-shadow: 5px 5px 10px #d4d4d4;
    }
    .login {
     text-decoration:underline;
    }
    .error__msg {
     text-align: center;
   }
}
</style>
