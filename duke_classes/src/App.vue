<template>
    <div id="app">
        <h1>{{ msg }}</h1>
        <div id = "login">
            <!--TO DO: INSERT LOGIN FIELDS AND BUTTONS-->
        </div>
        <div id = "welcome">
            <!--TO DO: SHOW LOGGED IN USER-->
        </div>
        <div id = "departments">
            <!--TO DO: DISPLAY DEPARTMENTS-->
        </div>
        <div id = "addDepartment">
            <!--TO DO: ALLOW ONLY USERS OR ADMIN TO ADD DEPARTMENTS-->
        </div>
        <div id = "editDepartment">
            <!--TO DO: ALLOW ONLY ADMIN TO EDIT DEPARTMENTS-->
        </div>
    </div>
</template>

<script>
import Firebase from 'firebase';
    
var config = {
    apiKey: "AIzaSyDGGjNGZez_-5G4zXfiyG79xoL4QuKAkdc",
    authDomain: "cs290-finalproject.firebaseapp.com",
    databaseURL: "https://cs290-finalproject.firebaseio.com",
    projectId: "cs290-finalproject",
    storageBucket: "",
    messagingSenderId: "123212092896"
  }

var db = Firebase.initializeApp(config).database();
var sampleDB = db.ref("sample");
    
export default {
    name: 'app',
    data () {
        return {
            msg: 'Welcome to Your Vue.js App'
        }
    },
    //connection to Firebase here
    firebase:{
        sample: sampleDB
    }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
