<template>
    <div id = "classReviews">
        <h1 id= "title">Class: {{departmentName}} {{classNumber}}</h1>
        <div id = "averageReviews">
            <!--TO DO: DISPLAY AVERAGE DIFFICULTY AND AVERAGE ENJOYMENT-->
            <ul>
                <li><h4>Average Enjoyment: {{avgEnjoyment(departmentName, classNumber)}}</h4></li>
                <li><h4>Average Difficulty: {{avgDifficulty(departmentName, classNumber)}}</h4></li>
            </ul>
        </div>
        <div id = "new" v-show = "currentUser != ''">
            <h3>Have an opinion? Write a review!</h3>
            <div v-show = "!showAddReview">
                <button v-on:click = "toggleAdd()">Write Review</button>
                <button v-on:click = "toggleAdd()">Update Your Review</button>
            </div>
            <div id = "reviewCreation" v-show = "showAddReview">
                <h4>Overall Enjoyment:</h4>
                <input type="radio" v-model="enjoyment" value = "0" id = "enj0">
                <label for = "enj0">0</label>
                <input type="radio" v-model="enjoyment" value = "1" id = "enj1">
                <label for = "enj1">1</label>
                <input type="radio" v-model="enjoyment" value = "2" id = "enj2">
                <label for = "enj2">2</label>
                <input type="radio" v-model="enjoyment" value = "3" id = "enj3">
                <label for = "enj3">3</label>
                <input type="radio" v-model="enjoyment" value = "4" id = "enj4">
                <label for = "enj4">4</label>
                <input type="radio" v-model="enjoyment" value = "5" id = "enj5">
                <label for = "enj5">5</label>
                <h4>Difficulty:</h4>
                <input type="radio" v-model="difficulty" value = "0" id = "dif0">
                <label for = "dif0">0</label>
                <input type="radio" v-model="difficulty" value = "1" id = "dif1">
                <label for = "dif1">1</label>
                <input type="radio" v-model="difficulty" value = "2" id = "dif2">
                <label for = "dif2">2</label>
                <input type="radio" v-model="difficulty" value = "3" id = "dif3">
                <label for = "dif3">3</label>
                <input type="radio" v-model="difficulty" value = "4" id = "dif4">
                <label for = "dif4">4</label>
                <input type="radio" v-model="difficulty" value = "5" id = "dif5">
                <label for = "dif5">5</label>
                <label for = "prof"><h4>Professor:</h4></label>
                <input id = "prof" v-model = "professor" placeholder="Enter Professor">
                <label for = "semesterTaken"><h4>Semester:</h4></label>
                <input id = "semesterTaken" v-model = "semester" placeholder="Enter Semester">
                <label for = "assign"><h4>Main Assignments:</h4></label>
                <textarea id = "assign" v-model = "assignments" placeholder = "Enter Assignments"></textarea>
                <label for = "thoughtsAndSuggestions"><h4>Overall Thoughts and Suggestions:</h4></label>
                <textarea id = "thoughtsAndSuggestions" v-model = "thoughts" placeholder = "Enter Thoughts"></textarea>
                <div></div>
                <input type="checkbox" id="checkbox" v-model="checked">
                <label for="checkbox">Show User Email</label>
                <button v-on:click = "newReview(), toggleAdd()">Submit Review</button>
                <h4 v-show="fieldsNotFilledOut">Please fill out all fields</h4>
            </div>
            <!--TO DO: ALLOW ONLY USERS OR ADMIN TO ADD A REVIEW-->
        </div>
        <div id = "reviews" >
            <h2>Reviews</h2>
            <h3>Filter Reviews:</h3>
            <input id = "filterEnj" @keyup.enter="orderByEnjoyment(departmentName, classNumber, enjoymentFilter), clearEdit()" v-model = "enjoymentFilter" placeholder="Show this enjoyment">
            <label for="filterEnj" class="visuallyhidden">Filter by this enjoyment number</label>
            <input id = "filterDif" @keyup.enter="filterByDifficulty(departmentName, classNumber, difficultyFilter), clearEdit()" v-model = "difficultyFilter" placeholder="Show this difficulty">
            <label for="filterDif" class="visuallyhidden">Filter by this difficulty number</label>
            <input id = "filterProf" @keyup.enter="filterByProfessor(departmentName, classNumber, professorFilter), clearEdit()" v-model = "professorFilter" placeholder="Show this professor">
            <label for="filterProf" class="visuallyhidden">Filter by this professor</label>
            <button v-on:click="resetFilters(departmentName, classNumber)">Reset Filters</button>
            <ul v-for = "element in currentClass">
                <li v-show = "element.show">
                    <h4>Overall Enjoyment: {{element.enjoyment}}</h4>
                    <h4>Difficulty: {{element.difficulty}}</h4>
                    <h4>Professor: {{element.professor}}</h4>
                    <h4>Main Assignments: {{element.assignments}}</h4>
                    <h4>Overall Thoughts and Suggestions: {{element.thoughts}}</h4>
                    <h4>Date Submitted: {{element.date}}</h4>
                    <h4>User: {{element.userShow}}</h4>
                    <h4>Votes: {{element.votes}}</h4>
                    <button v-on:click="upVote(departmentName, classNumber, element.votes, element.user)">Up Vote</button>
                    <button v-on:click="downVote(departmentName, classNumber, element.votes, element.user)">Down Vote</button>
                    <button v-on:click="removeReview">Delete Review</button>
                </li>
            </ul>
            <!--TO DO: DISPLAY ALL REVIEWS-->
        </div>
        <button v-on:click = "returnToHome">Return To Home Page</button>
    </div>
</template>
<script>
export default {
    name: "ClassReviews",
    props: ["currentClass", "addReview", "departmentName", "classNumber", "avgEnjoyment", "avgDifficulty", "currentUser", "userIsAdmin", "returnToHome", "orderByEnjoyment", "upVote", "downVote", "resetFilters" , "filterByDifficulty", "filterByProfessor", "deleteReview"],
    data(){
        return{
            enjoyment: "",
            difficulty: "",
            professor: "",
            semester: "",
            assignments: "",
            thoughts: "",
            checked: false,
            enjoymentEdit:"",
            enjoymentFilter:"",
            difficultyFilter:"",
            professorFilter:"",
            showAddReview: false,
            fieldsNotFilledOut: false
        }
    },
    methods:{
        clearEdit: function()
        {
            this.enjoyment = "";
            this.difficulty = "";
            this.professor="";
            this.semester="";
            this.assignments="";
            this.thoughts="";
            this.checked=false;
            this.enjoymentFilter="";
            this.difficultyFilter="";
            this.professorFilter="";
            
        },
        newReview: function()
        {
            if(this.checked == false)
                {
                    var show = "Anonymous";
                }
            else if(this.checked == true)
                {
                    var show = this.currentUser;
                }
            var reviewInfo = {
                enjoyment: this.enjoyment,
                difficulty: this.difficulty,
                professor: this.professor,
                semester: this.semester,
                assignments: this.assignments,
                thoughts: this.thoughts,
                date: new Date().toLocaleString(),
                user: this.currentUser,
                userShow: show,
                votes:0,
                show:true
            }
            var crop = this.currentUser.indexOf("@");
            var userCrop = this.currentUser.substring(0,crop);
            if(this.enjoyment!="" && this.difficulty!="" && this.professor!="" && this.semester!="" && this.assignments!="" && this.thoughts!="" && this.user!=""){
                this.addReview(this.departmentName, this.classNumber, userCrop, reviewInfo); 
                this.fieldsNotFilledOut=false;
                this.clearEdit();
            }
            else{
                this.fieldsNotFilledOut=true;
            }
        },
        removeReview:function(){
            var crop = this.currentUser.indexOf(".");
            var userCrop = this.currentUser.substring(0,crop);
            this.deleteReview(this.departmentName, this.classNumber, userCrop);  
        },
        toggleAdd: function()
        {
            this.showAddReview = !this.showAddReview;
        }
    }
}
</script>
<style>
    #averageReviews li{
        border: 2px solid #0000e6; 
        background-color: #0066ff;
        color: white;
        border-radius: 8px;
        padding: 5px;
    }
    #news{
        line-height: .05cm;
    }
    #reviews li{
        border: 5px solid white;  
        padding: 5px;
        border-radius: 12px;
    }
    #reviews h4{
        text-align: left;
    }
</style>