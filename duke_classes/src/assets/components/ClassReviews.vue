<template>
    <div id = "classReviews">
        <h1>Class Number</h1>
        <div id = "averageReviews">
            <!--TO DO: DISPLAY AVERAGE DIFFICULTY AND AVERAGE ENJOYMENT-->
        </div>
        <div id = "addReview">
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
            <button v-on:click = "newReview">Add Review</button>
            <!--TO DO: ALLOW ONLY USERS OR ADMIN TO ADD A REVIEW-->
        </div>
        <div id = "reviews" v-for = "element in currentClass.reviews">
            <h4>Overall Enjoyment: {{element.enjoyment}}</h4>
            <h4>Difficulty: {{element.difficulty}}</h4>
            <h4>Professor: {{element.professor}}</h4>
            <h4>Main Assignments: {{element.assignments}}</h4>
            <h4>Overall Thoughts and Suggestions: {{element.thoughts}}</h4>
            <!--TO DO: DISPLAY ALL REVIEWS-->
        </div>
        <div id = "editReviews">
            <!--TO DO: ALLOW ONLY ADMIN TO EDIT REVIEWS-->
        </div>
    </div>
</template>
<script>
export default {
    name: "ClassReviews",
    props: ["currentClass", "addReview", "departmentName", "classNumber"],
    data(){
        return{
            enjoyment: "",
            difficulty: "",
            professor: "",
            semester: "",
            assignments: "",
            thoughts: ""
        }
    },
    methods:{
        newReview: function()
        {
            var reviewInfo = {
                enjoyment: this.enjoyment,
                difficulty: this.difficulty,
                professor: this.professor,
                semester: this.semester,
                assignments: this.assignments,
                thoughts: this.thoughts
            }
            this.addReview(this.departmentName, this.classNumber, reviewInfo);
            
        }
    }
}
</script>
<style>
</style>