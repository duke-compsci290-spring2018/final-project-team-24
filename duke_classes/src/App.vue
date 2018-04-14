<template>
    <div id="app">
        <h1>{{ msg }}</h1>
        <div id = "login">
            <!--TO DO: INSERT LOGIN FIELDS AND BUTTONS-->
            <h1>Login here</h1>
            <button v-on:click = "addSample">Testing Sample Database</button>
        </div>
        <div id = "welcome">
            <!--TO DO: SHOW LOGGED IN USER-->
            <h1>Welcome the user</h1>
        </div>
        <!--HOME PAGE WITH ALL THE DEPARTMENTS-->
        <homePage></homePage>
        <!--DEPARTMENT PAGE-->
        <departmentPage></departmentPage>
        <!--CLASS REVIEWS PAGE-->
        <classReviews></classReviews>
        <!--ADMIN REQUESTS PAGE-->
        <adminRequests></adminRequests>
    </div>
</template>

<script>
    import Firebase from 'firebase'
    import HomePage from './assets/components/HomePage'
    import DepartmentPage from './assets/components/DepartmentPage'
    import ClassReviews from './assets/components/ClassReviews'
    import AdminRequests from './assets/components/AdminRequests'
    
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
        },
        //components
        components:{
            HomePage,
            DepartmentPage,
            ClassReviews,
            AdminRequests
        },
        //methods
        methods:{
            addSample: function(){
                sampleDB.push({text: "hello"});
            }
        }
    }
</script>

<style>
/*This is copied from the webpack simple and should be changed*/
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
