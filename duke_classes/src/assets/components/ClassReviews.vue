<template>
    <div id = "classReviews">
        <h1>{{departmentName}} {{classNumber}}</h1>
        <div id = "averageReviews">
            <!--TO DO: DISPLAY AVERAGE DIFFICULTY AND AVERAGE ENJOYMENT-->
            <h4>Average Enjoyment: {{avgEnjoyment(departmentName, classNumber)}}</h4>
            <h4>Average Difficulty: {{avgDifficulty(departmentName, classNumber)}}</h4>
        </div>
        <div id = "addReview" v-show = "currentUser != ''">
            <h4>Overall Enjoyment:</h4>
            <input type="radio" v-model="enjoyment" value = "0" id = "0">
            <label for = "0">0</label>
            <input type="radio" v-model="enjoyment" value = "1" id = "1">
            <label for = "1">1</label>
            <input type="radio" v-model="enjoyment" value = "2" id = "2">
            <label for = "2">2</label>
            <input type="radio" v-model="enjoyment" value = "3" id = "3">
            <label for = "3">3</label>
            <input type="radio" v-model="enjoyment" value = "4" id = "4">
            <label for = "4">4</label>
            <input type="radio" v-model="enjoyment" value = "5" id = "5">
            <label for = "5">5</label>
            <h4>Difficulty:</h4>
            <input type="radio" v-model="difficulty" value = "0" id = "0">
            <label for = "0">0</label>
            <input type="radio" v-model="difficulty" value = "1" id = "1">
            <label for = "1">1</label>
            <input type="radio" v-model="difficulty" value = "2" id = "2">
            <label for = "2">2</label>
            <input type="radio" v-model="difficulty" value = "3" id = "3">
            <label for = "3">3</label>
            <input type="radio" v-model="difficulty" value = "4" id = "4">
            <label for = "4">4</label>
            <input type="radio" v-model="difficulty" value = "5" id = "5">
            <label for = "5">5</label>
            <h4>Professor:</h4>
            <input v-model = "professor" placeholder="Enter Professor">
            <h4>Semester:</h4>
            <input v-model = "semester" placeholder="Enter Semester">
            <h4>Main Assignments:</h4>
            <textarea v-model = "assignments" placeholder = "Enter Assignments"></textarea>
            <h4>Overall Thoughts and Suggestions:</h4>
            <textarea v-model = "thoughts" placeholder = "Enter Thoughts"></textarea>
            <div></div>
            <input type="checkbox" id="checkbox" v-model="checked">
            <label for="checkbox">Show User Email</label>
            <button v-on:click = "newReview">Add Review</button>
            <!--TO DO: ALLOW ONLY USERS OR ADMIN TO ADD A REVIEW-->
        </div>
         <input @keyup.enter="orderByEnjoyment(departmentName, classNumber, enjoymentFilter)" v-model = "enjoymentFilter" placeholder="Show this enjoyment">
        <h4>Here</h4>
        <div id = "reviews" v-for = "element in currentClass">
            <h4 v-show = "element.show">Overall Enjoyment: {{element.enjoyment}}</h4>
            <h4 v-show = "element.show">Difficulty: {{element.difficulty}}</h4>
            <h4 v-show = "element.show">Professor: {{element.professor}}</h4>
            <h4 v-show = "element.show">Main Assignments: {{element.assignments}}</h4>
            <h4 v-show = "element.show">Overall Thoughts and Suggestions: {{element.thoughts}}</h4>
            <h4 v-show = "element.show">Date Submitted: {{element.date}}</h4>
            <h4 v-show = "element.show">User: {{element.userShow}}</h4>
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
            enjoymentFilter:""
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
            var crop = this.currentUser.indexOf(".");
            var userCrop = this.currentUser.substring(0,crop);
            this.addReview(this.departmentName, this.classNumber, userCrop, reviewInfo);   
        },
        changeReview: function()
        {
            this.editReview(this.departmentName, this.classNumber, this.enjoymentEdit);
        }
    }
}
</script>
<style>
</style>