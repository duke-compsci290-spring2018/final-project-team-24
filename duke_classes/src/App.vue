<template>
    <div id="app">
        <h1>{{ msg }}</h1>
        <div id = "login">
            <!--TO DO: INSERT LOGIN FIELDS AND BUTTONS-->
            <h1>Sign Up Here:</h1>
            <input v-model = "newUserEmail" placeholder="Enter Email">
            <input v-model = "newUserPassword" placeholder="Enter Password">
            <form id = "form" @submit.prevent = "storeImage">
                <label>Choose User Image:</label>
                <input type="file" id="files" name="files[]" />
            </form>
            <button v-on:click = "createUser">Create User</button>
            <h1>Already have an account? Login here:</h1>
            <input v-model = "loginEmail" placeholder="Enter Email">
            <input v-model = "loginPassword" placeholder="Enter Password">
            <button v-on:click = "login">Login</button>
            <h1 v-show = "userIsAdmin">Admin?</h1>
            <button v-show = "userIsAdmin" v-on:click = "showAdminRequests = true">Show Administrator Requests</button>
        </div>
        <div id = "welcome">
            <!--TO DO: SHOW LOGGED IN USER-->
            <h1>Welcome {{currentUser}}</h1>
            <img v-bind:src="userImage" width="25%" height="25%" v-show = "userImage != ''">
            <button v-on:click = "logout">Logout</button>
        </div>
        <!--HOME PAGE WITH ALL THE DEPARTMENTS-->
        <homePage v-show = "showHomePage"
                  :departments = "departments" 
                  :addDepartment = "addDepartment" 
                  :changeCurrentDepartment = "changeCurrentDepartment"
                  :editDepartment = "editDepartment"
                  :currentUser = "currentUser"
                  :userIsAdmin = "userIsAdmin"
                  >
        </homePage>
        <!--DEPARTMENT PAGE-->
        <departmentPage v-show = "showDepartmentPage" 
                        :addClass = "addClass"
                        :currentDepartment = "currentDepartment"
                        :addDepartment = "addDepartment"
                        :editClasses="editClasses"
                        :changeCurrentClass="changeCurrentClass"
                        :currentUser = "currentUser"
                        :userIsAdmin = "userIsAdmin"
                        :returnToHome = "returnToHome"
                        :deleteDepartment= "deleteDepartment"
        >
        </departmentPage>
        <!--CLASS REVIEWS PAGE-->
        <classReviews v-show = "showClassReviews"
                      :currentClass = "currentClass"
                      :addReview = "addReview"
                      :departmentName = "currentDepartmentName"
                      :classNumber = "currentClassNumber"
                      :avgEnjoyment = "avgEnjoyment"
                      :avgDifficulty = "avgDifficulty"
                      :currentUser = "currentUser"
                      :userIsAdmin = "userIsAdmin"
                      :returnToHome = "returnToHome"
                      :orderByEnjoyment= "orderByEnjoyment"
                      :upVote="upVote"
                      :downVote="downVote"
                        >
        </classReviews>
        <!--ADMIN REQUESTS PAGE-->
        <adminRequests v-show = "showAdminRequests"
                       :users = "users"
                       :returnToHome = "returnToHome">
        </adminRequests>
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
        storageBucket: "cs290-finalproject.appspot.com",
        messagingSenderId: "123212092896"
      }

    var db = Firebase.initializeApp(config).database();
    var auth = Firebase.auth();
    var departmentsWithClasses = db.ref("departments");
    var classesWithReviews = db.ref('classes');
    var reviewsRef = db.ref('reviews');
    var usersRef = db.ref('users');
    var storageRef = Firebase.storage().ref();

    export default {
        name: 'app',
        data () {
            return {
                msg: 'Welcome to Your Vue.js App',
                currentDepartment: [],
                currentClass: [],
                currentDepartmentName: "",
                currentClassNumber: "",
                reviewKey: "",
                newUserEmail: "",
                newUserPassword: "",
                loginEmail: "",
                loginPassword: "",
                currentUser: "",
                userIsAdmin: "",
                userImage: "",
                showAdminRequests: false,
                showClassReviews: false,
                showDepartmentPage: false,
                showHomePage: true
            }
        },
        //connection to Firebase here
        firebase:{
            departments: departmentsWithClasses.orderByChild("name"),
            classes: classesWithReviews.orderByChild("number"),
            reviews: reviewsRef,
            users: usersRef
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
            testing: function()
            {
                function compare(a, b) {
                  if (a.enjoyment < b.enjoyment)
                    return -1;
                  if (a.enjoyment > b.enjoyment)
                    return 1;
                  return 0;
                }
                console.log(this.currentClass);
    //            return this.arrays.sort(Object.values(this.currentClass));
                for(var item in this.currentClass)
                    {
                        console.log(item);
                        reviewsRef.child(item).orderByChild("enjoyment").on("child_added", function(data) {
                                console.log(data.val().name)});
                    }
                
//                console.log(Object.values(this.currentClass).sort(compare));
            },
            createUser: function()
            {
                var input = document.getElementById('files');
                var file = input.files[0];
                var email = this.newUserEmail;
                if(this.newUserEmail.includes("@duke.edu"))
                    {
                        usersRef.push({
                            email: this.newUserEmail,
                            password: this.newUserPassword,
                            admin: true,
                            submittedAdminRequest: false
                        });
                        storageRef.child('images/' + file.name)
                                .put(file)
                                .then(snapshot =>  this.addUserImage(email, snapshot.downloadURL));
                    }
                this.newUserEmail = "";
                this.newUserPassword = "";
                input.value = "";
            },
            addUserImage (user, url) {
                console.log("here");
                
                for(var i = 0; i <this.users.length; i ++)
                    {
                        if(this.users[i].email == user)
                            {
                                console.log("hereagain");
                                console.log(url);
                                usersRef.child(this.users[i]['.key']).update({image: url});
                            }
                    }
                
            },
            login: function()
            {
                for(var i = 0; i <this.users.length; i ++)
                    {
                        if(this.users[i].email == this.loginEmail && this.users[i].password == this.loginPassword)
                            {
                                this.currentUser = this.users[i].email;
                                this.userIsAdmin = this.users[i].admin;
                                this.userImage = this.users[i].image;
                            }
                    }
                this.loginEmail = "";
                this.loginPassword = "";     
            },
            logout: function()
            {
                this.currentUser = "";
                this.userIsAdmin = false;
                this.userImage = "";
            },
            grantAdmin: function(userEmail)
            {
                for(var i = 0; i< this.users.length;i++)
                    {
                        if(this.users[i].email == userEmail)
                            {
                                usersRef.child(this.users[i]['.key']).update({admin: true});
                            }
                    }
            },
            returnToHome: function()
            {
                this.showAdminRequests = false;
                this.showClassReviews = false;
                this.showDepartmentPage = false;
                this.showHomePage = true;  
            },
            addDepartment: function(department)
            {
                departmentsWithClasses.push({name: department});
            },
            deleteDepartment: function(department){
                for(var i=0; i<this.departments.length; i++){
                    if(this.departments[i].name==department){

                            var hasClasses=true;
                            var count=0; 
                            while(hasClasses){
                                var hasMoreClasses=false; 
                               // console.log(this.classes.length); 
                                //console.log(count); 
                                for(var k=0; k< this.classes.length; k++){
                                    
                                    if(this.classes[k].class.includes(department)){
                                            reviewsRef.child(this.classes[k].reviews).remove(); 
                                        classesWithReviews.child(this.classes[k]['.key']).remove(); 
                                        hasMoreClasses=true; 
                                        break;
                                    }
                                }
                                if(hasMoreClasses==false){
                                    hasClasses=false;
                                }
                                count++;
                            }
                            
                        departmentsWithClasses.child(this.departments[i]['.key']).remove();
                    }
                }  
            },
            addClass: function(department, classNumber)
            {
                for(var i  = 0; i < this.departments.length; i++)
                    {
                        console.log(this.departments[i].name)
                        if(this.departments[i].name == department)
                            {
                                //create a reference for future reviews
                                var key = reviewsRef.push({show:false}).key;
                                //creates a class under the departments ref
                                departmentsWithClasses.child(this.departments[i]['.key']).child('classes').child(classNumber).update({number: classNumber});
                                //creates a reference for the class in the class ref
                                classesWithReviews.push({class: department+classNumber, reviews: key});
                                //changes currentdepartment
                                this.changeCurrentDepartment(this.departments[i]);
                                
                            }
                    }
                
                
            },
            editDepartment: function(department, newName)
            {
                for(var i  = 0; i < this.departments.length; i++)
                    {
                        if(this.departments[i].name == department)
                            {
                                departmentsWithClasses.child(this.departments[i]['.key']).update({name: newName});
                            }
                    }
                for(var j = 0; j < this.classes.length; j++)
                    {
                        if(this.classes[j].class.includes(department))
                            {
                                var newClassName = newName + this.classes[j].class.substring(department.length);
                                classesWithReviews.child(this.classes[j]['.key']).update({class: newClassName});
                            }
                    }
            },
            editClasses: function(number, newNumber, department){
                for(var i=0; i<this.departments.length; i++){
                    if(this.departments[i].name==department)
                    {
                        departmentsWithClasses.child(this.departments[i]['.key']).child('classes').child(number).remove();
                        departmentsWithClasses.child(this.departments[i]['.key']).child('classes').child(newNumber).update({number:newNumber});
                        
                        for(var j=0; j<this.classes.length; j++)
                        {
                            if(this.classes[j].class==department+number){
                                classesWithReviews.child(this.classes[j]['.key']).update({class: department+ newNumber});
                            }
                        }
                        
                        this.changeCurrentDepartment(this.departments[i]);
  
                    }
                }  
            },
            changeCurrentDepartment(department)
            {
                this.currentDepartment = department;
                this.showAdminRequests = false;
                this.showClassReviews = false;
                this.showDepartmentPage = true;
                this.showHomePage = false; 
            },
            changeCurrentClass(department, number)
            {
                for(var j=0; j<this.classes.length; j++)
                    {
                        if(this.classes[j].class==department+number)
                        {
                            var currentKey = this.classes[j].reviews;
                            for (var k = 0; k < this.reviews.length; k++)
                                {
                                    if(this.reviews[k]['.key'] == currentKey)
                                        {
                                            this.currentClass = this.reviews[k];
                                            this.reviewKey = currentKey;
                                        }
                                }
                            this.currentDepartmentName = department;
                            this.currentClassNumber = number;
                        }
                    }
                this.showAdminRequests = false;
                this.showClassReviews = true;
                this.showDepartmentPage = false;
                this.showHomePage = false;
                
            },
            addReview(department, classNumber, user, reviewInfo)
            {
                for(var j=0; j<this.classes.length; j++)
                    {
                        if(this.classes[j].class==department+classNumber)
                        {
                            //adds the review to the review reference
                            reviewsRef.child(this.classes[j].reviews).child(user).update(reviewInfo);
                            //changes current class
                        }
                    }
                this.changeCurrentClass(department, classNumber);
            },
             upVote(department, classNumber, votes, user){
                for(var j=0; j<this.classes.length; j++)
                    {
                        if(this.classes[j].class==department+classNumber)
                        {
                            var reviewKey = this.classes[j].reviews;
                            
                            for(var i = 0; i < this.reviews.length; i ++)
                                {
                                    if(this.reviews[i]['.key'] == reviewKey)
                                        {
                                            for(var element in this.reviews[i]){
                                                if(element.user==user){
                                                    element.votes+=1;
                                                    }
                                            }           
                                        }
                                }
                            }
                        }
                },
                   
            downVote(department, classNumber, votes, user){
                for(var j=0; j<this.classes.length; j++)
                    {
                        if(this.classes[j].class==department+classNumber)
                        {
                            var reviewKey = this.classes[j].reviews;
                            
                            for(var i = 0; i < this.reviews.length; i ++)
                                {
                                    if(this.reviews[i]['.key'] == reviewKey)
                                        {
                                            for(var element in this.reviews[i]){
                                                if(element.user==user){
                                                    element.votes-=1;
                                                    if(element.votes<-10){
                                                        reviewsRef.child(this.classes[j].reviews).child(user).remove();
                                                    }
                                                    }
                                            }
                                                       
                                                       
                                        }
                                }
                            }
                        }
            },
            editReview(department, classNumber, enjoyment){
                console.log("HERE");
                console.log
                for(var j=0; j<this.classes.length; j++)
                    {
                        if(this.classes[j].class==department+classNumber)
                        {   

                            for(var i=0; i<this.classes[j].reviews.length; i++){
                                if(this.classes[j].reviews[i].assignments=="big"){
                                    console.log("HERE");
                                    classesWithReviews.child(this.classes[j]['.key']).child('reviews').child(this.classes[j].reviews[i]['.key']).update({enjoyment:enjoyment});
                                    this.changeCurrentClass(department, classNumber);
                                }
                                
                                
                            }
                        }
                    }
            },
            avgEnjoyment(department, classNumber)
            {
                var enj = 0;
                var count = 0;
                for(var j=0; j<this.classes.length; j++)
                    {
                        if(this.classes[j].class==department+classNumber)
                        {
                            var reviewKey = this.classes[j].reviews;
                            
                            for(var i = 0; i < this.reviews.length; i ++)
                                {
                                    if(this.reviews[i]['.key'] == reviewKey)
                                        {
                                            for(var element in this.reviews[i])
                                                {
                                                    if(element != ".key" && element != "show")
                                                   { 
                                                       reviewsRef.child(reviewKey).child(element).child('enjoyment').once('value', function(snapshot){
                                                        enj += parseInt(snapshot.val());
                                                       });
                                                       count += 1;
                                                   }
                                                }
                                        }
                                }
                        }
                    }
                if(count > 0)
                    {
                       return enj/count; 
                    }
                else
                    {
                        return "N/A";
                    }
                
            },
            avgDifficulty(department, classNumber)
            {
                var dif = 0;
                var count = 0;
                for(var j=0; j<this.classes.length; j++)
                    {
                        if(this.classes[j].class==department+classNumber)
                        {
                            var reviewKey = this.classes[j].reviews;
                            
                            for(var i = 0; i < this.reviews.length; i ++)
                                {
                                    if(this.reviews[i]['.key'] == reviewKey)
                                        {
                                            for(var element in this.reviews[i])
                                                {
                                                    if(element != ".key" && element != "show")
                                                   { 
                                                       reviewsRef.child(reviewKey).child(element).child('difficulty').once('value', function(snapshot){
                                                        dif += parseInt(snapshot.val());
                                                       });
                                                       count += 1;
                                                   }
                                                }
                                        }
                                }
                        }
                    }
                if(count > 0)
                    {
                       return dif/count; 
                    }
                else
                    {
                        return "N/A";
                    }
            },
            orderByEnjoyment(departmentNumber, classNumber, enjoymentFilter){
                //console.log(departmentNumber+classNumber);
                //console.log(this.currentClass);
                console.log(enjoymentFilter)
                for(var j=0; j<this.classes.length; j++)
                    {
                        if(this.classes[j].class==department+classNumber)
                        {
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
