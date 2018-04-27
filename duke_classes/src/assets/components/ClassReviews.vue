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
                <h4><label for = "prof">Professor:</label></h4>
                <input id = "prof" v-model = "professor" placeholder="Enter Professor">
                <h4><label for = "semesterTaken">Semester:</label></h4>
                <input id = "semesterTaken" v-model = "semester" placeholder="Enter Semester">
                <h4><label for = "assign">Main Assignments:</label></h4>
                <textarea id = "assign" v-model = "assignments" placeholder = "Enter Assignments"></textarea>
                <h4><label for = "thoughtsAndSuggestions">Overall Thoughts and Suggestions:</label></h4>
                <textarea id = "thoughtsAndSuggestions" v-model = "thoughts" placeholder = "Enter Thoughts"></textarea>

                <div></div>
                <input type="checkbox" id="checkbox" v-model="checked">
                <label for="checkbox">Show User Email</label>
                <button v-on:click = "newReview(), toggleAdd()">Add Review</button>
            </div>
            <!--TO DO: ALLOW ONLY USERS OR ADMIN TO ADD A REVIEW-->
        </div>
        <div id = "reviews" >
            <h2>Reviews</h2>
            <input @keyup.enter="orderByEnjoyment(departmentName, classNumber, enjoymentFilter)" v-model = "enjoymentFilter" placeholder="Show this enjoyment">
            <ul v-for = "element in currentClass">
                <li v-show = "element.show">
                    <h4>Overall Enjoyment: {{element.enjoyment}}</h4>
                    <h4>Difficulty: {{element.difficulty}}</h4>
                    <h4>Professor: {{element.professor}}</h4>
                    <h4>Main Assignments: {{element.assignments}}</h4>
                    <h4>Overall Thoughts and Suggestions: {{element.thoughts}}</h4>
                    <h4>Date Submitted: {{element.date}}</h4>
                    <h4>User: {{element.userShow}}</h4>
                </li>
            </ul>
            
            <!--TO DO: DISPLAY ALL REVIEWS-->
        </div>
        <div id = "editReviews">
            <h2>Edit reviews</h2>
            <h4>Overall Enjoyment:</h4>
            <input type="radio" v-model="enjoymentEdit" value = "0" id = "0">
            <label for = "0">0</label>
            <input type="radio" v-model="enjoymentEdit" value = "1" id = "1">
            <label for = "1">1</label>
            <input type="radio" v-model="enjoymentEdit" value = "2" id = "2">
            <label for = "2">2</label>
            <input type="radio" v-model="enjoymentEdit" value = "3" id = "3">
            <label for = "3">3</label>
            <input type="radio" v-model="enjoymentEdit" value = "4" id = "4">
            <label for = "4">4</label>
            <input type="radio" v-model="enjoymentEdit" value = "5" id = "5">
            <label for = "5">5</label>
            
            <button v-on:click = "changeReview">Edit Review</button>
            <!--TO DO: ALLOW ONLY ADMIN TO EDIT REVIEWS-->
        </div>
        <button v-on:click = "returnToHome">Return To Home Page</button>
    </div>
</template>
<script>
export default {
    name: "ClassReviews",
    props: ["currentClass", "addReview", "departmentName", "classNumber", "avgEnjoyment", "avgDifficulty", "currentUser", "userIsAdmin", "returnToHome", "editReview", "orderByEnjoyment", "upVote", "downVote"],
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
            showAddReview: false
        }
    },
    methods:{
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
            this.addReview(this.departmentName, this.classNumber, userCrop, reviewInfo);   
        },
        changeReview: function()
        {
            this.editReview(this.departmentName, this.classNumber, this.enjoymentEdit);
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