<template>
    <div id="app">
        <h1>{{ msg }}</h1>
        <div id = "login">
            <!--TO DO: INSERT LOGIN FIELDS AND BUTTONS-->
            <h1>Login here</h1>
            <button v-on:click = "addDepartment('spanish')">Add Department</button>
            <button v-on:click = "addClass('spanish','306')">Add Class</button>
            <button v-on:click="editClasses('306', '204', 'spanish')">Edit class</button>
            <button v-on:click="addReview('spanish', '220', 'hello')">EAddReview</button>
            <div v-for = "element in departments">
                <h3>{{element.name}}</h3>
            </div>
        </div>
        <div id = "welcome">
            <!--TO DO: SHOW LOGGED IN USER-->
            <h1>Welcome the user</h1>
        </div>
        <!--HOME PAGE WITH ALL THE DEPARTMENTS-->
        <homePage 
                  :departments = "departments" 
                  :addDepartment = "addDepartment" 
                  :changeCurrentDepartment = "changeCurrentDepartment"
                  :editDepartment = "editDepartment"
                  >
        </homePage>
        <!--DEPARTMENT PAGE-->
        <departmentPage 
                        :addClass = "addClass"
                        :currentDepartment = "currentDepartment"
                        :addDepartment = "addDepartment"
                        :editClasses="editClasses"
                        :changeCurrentClass="changeCurrentClass"
        >
        </departmentPage>
        <!--CLASS REVIEWS PAGE-->
        <classReviews
                      :currentClass = "currentClass"
                      :addReview = "addReview"
                      :departmentName = "currentDepartmentName"
                      :classNumber = "currentClassNumber">
        </classReviews>
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
    var departmentsWithClasses = db.ref("departments");
    var classesWithReviews = db.ref('classes');

    export default {
        name: 'app',
        data () {
            return {
                msg: 'Welcome to Your Vue.js App',
                currentDepartment: [],
                currentClass: [],
                currentDepartmentName: "",
                currentClassNumber: ""
            }
        },
        //connection to Firebase here
        firebase:{
            sample: sampleDB,
            departments: departmentsWithClasses,
            classes: classesWithReviews
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
            },
            addDepartment: function(department)
            {
                departmentsWithClasses.push({name: department});
            },
            addClass: function(department, classNumber)
            {
                for(var i  = 0; i < this.departments.length; i++)
                    {
                        console.log(this.departments[i].name)
                        if(this.departments[i].name == department)
                            {
                                departmentsWithClasses.child(this.departments[i]['.key']).child('classes').child(classNumber).update({number: classNumber});
                                classesWithReviews.push({class: department+classNumber});
                                this.changeCurrentDepartment(this.departments[i]);
                            }
                    }
                
            },
            editDepartment: function(department, newName)
            {
                for(var i  = 0; i < this.departments.length; i++)
                    {
                        console.log(this.departments[i].name)
                        if(this.departments[i].name == department)
                            {
                                departmentsWithClasses.child(this.departments[i]['.key']).update({name: newName});
                            }
                    }
            },
            editClasses: function(number, newNumber, department){
                for(var i=0; i<this.departments.length; i++){
                    if(this.departments[i].name==department)
                    {
                        console.log(this.departments[i].name);
                        departmentsWithClasses.child(this.departments[i]['.key']).child('classes').child(number).remove();
                        departmentsWithClasses.child(this.departments[i]['.key']).child('classes').child(newNumber).update({number:newNumber});
                        
                        for(var j=0; j<this.classes.length; j++)
                        {
                            if(this.classes[j].class==department+number){
                                classesWithReviews.child(this.classes[j]['.key']).update({class: department+ newNumber});
                            }
                        }
  
                    }
                }  
            },
            changeCurrentDepartment(department)
            {
                this.currentDepartment = department;
            },
            changeCurrentClass(department, number)
            {
                for(var j=0; j<this.classes.length; j++)
                    {
                        if(this.classes[j].class==department+number)
                        {
                            this.currentClass = this.classes[j];
                            this.currentDepartmentName = department;
                            this.currentClassNumber = number;
                        }
                    }
                
            },
            addReview(department, classNumber, reviewInfo)
            {
                for(var j=0; j<this.classes.length; j++)
                    {
                        if(this.classes[j].class==department+classNumber)
                        {
                            classesWithReviews.child(this.classes[j]['.key']).child('reviews').push(reviewInfo);
                            this.changeCurrentClass(department, classNumber);
                        }
                    }
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
