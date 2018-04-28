<template>
    <div id="app">
        <h1 id= "title">RateMyDukeClasses</h1>
        <ul>
            <li>
                <div id = "welcome">
                    <!--TO DO: SHOW LOGGED IN USER-->
                    <img v-bind:src="userImage" v-show = "userImage != ''" alt = "User's Profile Picture">
                    <h1>Welcome {{currentUser}}!</h1>
                    <button v-on:click = "requestAdmin(currentUser)" v-show = "!userIsAdmin && currentUser != ''">Request Administrator Status</button>
                </div>
            </li>
            <li>
                <div id = "login">
                    <!--TO DO: INSERT LOGIN FIELDS AND BUTTONS-->
                    <h3>Sign Up Here:</h3>
                    <input id = "newEmail" v-model = "newUserEmail" placeholder="netID@duke.edu">
                    <label for="newEmail" class="visuallyhidden">New User Email (netid@duke.edu)</label>
                    <input id = "newPassword" v-model = "newUserPassword" placeholder="Enter Password">
                    <label for="newPassword" class="visuallyhidden">New User Password</label>
                    <form id = "form" @submit.prevent = "storeImage">
                        <p><label for = "files">Choose User Image:</label>
                        <input type="file" id="files" name="files[]" /></p>
                    </form>
                    <button v-on:click = "createUser">Create User</button>
                    <h3>Already have an account? Login here:</h3>
                    <input id = "existingEmail" v-model = "loginEmail" placeholder="Enter Email">
                    <label for="existingEmail" class="visuallyhidden">Existing Email for login</label>
                    <input id = "existingPassword" v-model = "loginPassword" placeholder="Enter Password">
                    <label for="existingPassword" class="visuallyhidden">Existing Email for login</label>
                    <button v-on:click = "login">Login</button>
                    <h3 v-show = "userIsAdmin">Admin?</h3>
                    <button v-show = "userIsAdmin" v-on:click = "adminPage">Show Administrator Requests</button>
                </div>
            </li>
            
        </ul>
        <!--ADMIN REQUESTS PAGE-->
        <adminRequests v-show = "showAdminRequests"
                       :users = "users"
                       :grantAdmin = "grantAdmin"
                       :returnToHome = "returnToHome">
        </adminRequests>
        <!--HOME PAGE WITH ALL THE DEPARTMENTS-->
        <homePage v-show = "showHomePage"
                  :departments = "departments" 
                  :addDepartment = "addDepartment" 
                  :changeCurrentDepartment = "changeCurrentDepartment"
                  :editDepartment = "editDepartment"
                  :deleteDepartment="deleteDepartment"
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
                        :deleteClass= "deleteClass"
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
                      :resetFilters="resetFilters"
                      :filterByDifficulty="filterByDifficulty"
                      :filterByProfessor="filterByProfessor"
                      :deleteReview= "deleteReview"
                        >
        </classReviews>
        <h3 id = "logout">Not you? <button v-on:click = "logout">Logout</button></h3>
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
                            admin: false,
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
                for(var i = 0; i <this.users.length; i ++)
                    {
                        if(this.users[i].email == user)
                            {
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
                                usersRef.child(this.users[i]['.key']).update({admin: true, submittedAdminRequest: false});
                            }
                    }
            },
            requestAdmin: function(userEmail)
            {
                for(var i = 0; i< this.users.length;i++)
                    {
                        if(this.users[i].email == userEmail)
                            {
                                usersRef.child(this.users[i]['.key']).update({submittedAdminRequest: true});
                            }
                    }
            },
            adminPage: function()
            {
                this.showAdminRequests = true;
                this.showClassReviews = false;
                this.showDepartmentPage = false;
                this.showHomePage = false;    
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
                        console.log(this.departments[i].name);
                        departmentsWithClasses.child(this.departments[i]['.key']).child('classes').child(number).remove();
                        departmentsWithClasses.child(this.departments[i]['.key']).child('classes').child(newNumber).update({number:newNumber});
                        
                        for(var j=0; j<this.classes.length; j++)
                        {
                            if(this.classes[j].class==department+number){
                                classesWithReviews.child(this.classes[j]['.key']).update({class: department+ newNumber});
                                this.changeCurrentDepartment(this.departments[i]);
                            }
                        }
  
                    }
                }    
            },
            deleteClass: function(number, department){
                var departmentIndex=0;
                for(var i=0; i<this.departments.length; i++){
                    if(this.departments[i].name==department){
                        departmentsWithClasses.child(this.departments[i]['.key']).child('classes').child(number).remove();
                        departmentIndex=i;
                    }
                }
                var reviewKey="";
                for(var i=0; i<this.classes.length; i++){
                    if(this.classes[i].class==department+number){
                        reviewKey=this.classes[i].reviews;
                        classesWithReviews.child(this.classes[i]['.key']).remove();
                    }
                }
                console.log(reviewKey);
                reviewsRef.child(reviewKey).remove(); 
                this.changeCurrentDepartment(this.departments[departmentIndex]);
                
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
            deleteReview: function(department, classNumber, user){
              for(var i=0; i<this.classes.length; i++){
                  if(this.classes[i].class==department+classNumber){
                      //removes reivew from review reference
                      reviewsRef.child(this.classes[i].reviews).child(user).remove();
                  }
              } 
                this.changeCurrentClass(department, classNumber);
            },
            upVote: function(department, classNumber, votes, user){
                console.log(user);
                for(var j=0; j<this.classes.length; j++)
                    {
                        if(this.classes[j].class==department+classNumber)
                        {
                            var reviewKey = this.classes[j].reviews;
                            
                            for(var i = 0; i < this.reviews.length; i ++)
                                {
                                    if(this.reviews[i]['.key'] == reviewKey)
                                        {
                                            console.log("HERE");
                                            for(var element in this.reviews[i]){
                                                if(element != ".key")
                                                   { 
                                                var curUser="";
                                                    reviewsRef.child(reviewKey).child(element).child('user').once('value', function(snapshot){
                                                        curUser=snapshot.val();
                                                       });
                                                if(curUser==user){
                                                    var tempVotes=0;
                                                    reviewsRef.child(reviewKey).child(element).child('votes').once('value', function(snapshot){
                                                        tempVotes=snapshot.val();
                                                       });
                                                    tempVotes++;
                                                    reviewsRef.child(reviewKey).child(element).update({votes: tempVotes});
                                                    }
                                            }
                                                       
                                            }
                                        }
                                }
                            }
                        }
                this.changeCurrentClass(department, classNumber);
                },
                   
            downVote: function(department, classNumber, votes, user){
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
                                                if(element != ".key")
                                                   { 
                                                var curUser="";
                                                    reviewsRef.child(reviewKey).child(element).child('user').once('value', function(snapshot){
                                                        curUser=snapshot.val();
                                                       });
                                                if(curUser==user){
                                                    var tempVotes=0;
                                                    reviewsRef.child(reviewKey).child(element).child('votes').once('value', function(snapshot){
                                                        tempVotes=snapshot.val();
                                                       });
                                                    tempVotes--;
                                                    reviewsRef.child(reviewKey).child(element).update({votes: tempVotes});
                                                    
                                                    var checkVotes=0;
                                                    reviewsRef.child(reviewKey).child(element).child('votes').once('value', function(snapshot){
                                                        checkVotes=snapshot.val();
                                                       });
                                                    if(checkVotes<=-10){
                                                        reviewsRef.child(reviewKey).child(element).remove();
                                                    }
                                                    }
                                            }
                                                       
                                            }
                                                       
                                                       
                                        }
                                }
                            }
                        }
                 this.changeCurrentClass(department, classNumber);
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
            resetFilters: function(department, classNumber){
                for(var j=0; j<this.classes.length; j++)
                    {
                        if(this.classes[j].class==department+classNumber)
                        {
                            var reviewKey = this.classes[j].reviews;
                            
                            for(var i = 0; i < this.reviews.length; i ++)
                                {
                                    if(this.reviews[i]['.key'] == reviewKey)
                                        {
                                            console.log(reviewKey);
                                           
                                            for(var element in this.reviews[i])
                                                {
                                                    if(element != ".key" && element != "show")
                                                   { 
                                                      reviewsRef.child(reviewKey).child(element).update({show:true}); 
                                                   }
                                                }
                                        }
                                }
                        }
                    }
                 this.changeCurrentClass(department, classNumber);
            },
            orderByEnjoyment: function(department, classNumber, enjoymentFilter){
                this.resetFilters(department, classNumber);
                //console.log(departmentNumber+classNumber);
                //console.log(this.currentClass);
                console.log(enjoymentFilter)
                for(var j=0; j<this.classes.length; j++)
                    {
                        if(this.classes[j].class==department+classNumber)
                        {
                            var reviewKey = this.classes[j].reviews;
                            
                            for(var i = 0; i < this.reviews.length; i ++)
                                {
                                    if(this.reviews[i]['.key'] == reviewKey)
                                        {
                                            console.log(reviewKey);
                                           
                                            for(var element in this.reviews[i])
                                                {
                                                    if(element != ".key" && element != "show")
                                                   { 
                                                    var curEnjoyment=0;
                                                    reviewsRef.child(reviewKey).child(element).child('enjoyment').once('value', function(snapshot){
                                                        curEnjoyment=snapshot.val();
                                                       });
                                                       console.log(curEnjoyment);
                                                    if(parseInt(curEnjoyment) !=enjoymentFilter)
                                                   { 
                                                       console.log("HERE");
                                                     reviewsRef.child(reviewKey).child(element).update({show:false});
                                                        
                                                   }
                                                       else{
                                                           reviewsRef.child(reviewKey).child(element).update({show:true});
                                                       }
                                                   }
                                                }
                                        }
                                }
                        }
                    }
                this.changeCurrentClass(department, classNumber);

            },
            filterByDifficulty: function(department, classNumber, difficultyFilter){
                this.resetFilters(department, classNumber);
 
                for(var j=0; j<this.classes.length; j++)
                    {
                        if(this.classes[j].class==department+classNumber)
                        {
                            var reviewKey = this.classes[j].reviews;
                            
                            for(var i = 0; i < this.reviews.length; i ++)
                                {
                                    if(this.reviews[i]['.key'] == reviewKey)
                                        {
                                           // console.log(reviewKey);
                                           
                                            for(var element in this.reviews[i])
                                                {
                                                    if(element != ".key" && element != "show")
                                                   { 
                                                    var curDifficulty=0;
                                                    reviewsRef.child(reviewKey).child(element).child('difficulty').once('value', function(snapshot){
                                                        curDifficulty=snapshot.val();
                                                       });
                                                       console.log(curDifficulty);
                                                    if(parseInt(curDifficulty) !=difficultyFilter)
                                                   { 
                                                       console.log("HERE");
                                                     reviewsRef.child(reviewKey).child(element).update({show:false});
                                                        
                                                   }
                                                       else{
                                                           reviewsRef.child(reviewKey).child(element).update({show:true});
                                                       }
                                                   }
                                                }
                                        }
                                }
                        }
                    }
                this.changeCurrentClass(department, classNumber);
            },
            filterByProfessor: function(department, classNumber, professorFilter){
                this.resetFilters(department, classNumber);
                for(var j=0; j<this.classes.length; j++)
                    {
                        if(this.classes[j].class==department+classNumber)
                        {
                            var reviewKey = this.classes[j].reviews;
                            
                            for(var i = 0; i < this.reviews.length; i ++)
                                {
                                    if(this.reviews[i]['.key'] == reviewKey)
                                        {
                                           // console.log(reviewKey);
                                           
                                            for(var element in this.reviews[i])
                                                {
                                                    if(element != ".key")
                                                   { 
                                                    var curProfessor="";
                                                    reviewsRef.child(reviewKey).child(element).child('professor').once('value', function(snapshot){
                                                        curProfessor=snapshot.val();
                                                       });
                                                       //console.log(curDifficulty);
                                                    if(curProfessor !=professorFilter)
                                                   { 
                                                       console.log("HERE");
                                                     reviewsRef.child(reviewKey).child(element).update({show:false});
                                                        
                                                   }
                                                       else{
                                                           reviewsRef.child(reviewKey).child(element).update({show:true});
                                                       }
                                                   }
                                                }
                                        }
                                }
                        }
                    }
                this.changeCurrentClass(department, classNumber);
            }
        }
    }
</script>

<style>
/*This is copied from the webpack simple and should be changed*/
    body{
        background-color: gray;
        background-image: url("https://img.etsystatic.com/il/b4b112/804784173/il_570xN.804784173_7kpm.jpg?version=0");
        background-repeat: repeat;
        background-size: 5%;
    }
    #app {
        font-family: "Trebuchet MS", Helvetica, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #004d99;
/*        text-shadow: 1px 1px #595959;*/
        margin: 5%;
        background-color: #e6e6e6;

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
    #title{
        font-size: 60px;
        margin-top: 5px;
        color: #0000e6;
    }
    #login{
        display: inline-block;
        border: 3px solid black;
        padding: 5px;
/*        width: 40%;*/
        color: #595959;
/*        margin-bottom: 25%;*/
/*        margin-left: 55%;*/
    }
    #welcome{
        display: inline-block;
        padding: 5px;
/*        width: 50%;*/
        margin-top: 10px;
/*        margin-right: 45%;*/
        text-align: center;
/*        border: 3px solid black;*/
        
    }
    #welcome img{
        width: 20%;
        height: 20%;
        
    }
    #logout{
        color: #595959;
        padding: 5px;
        text-align: left;
    }
    /*This CSS styling is taken from https://www.w3.org/WAI/tutorials/forms/labels/#note-on-hiding-elements 
    to improve the accessiblity of our website*/
    .visuallyhidden {
      border: 0;
      clip: rect(0 0 0 0);
      height: 1px;
      margin: -1px;
      overflow: hidden;
      padding: 0;
      position: absolute;
      width: 1px;
    }
</style>
