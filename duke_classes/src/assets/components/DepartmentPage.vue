<template>
    <div id = "departmentPage">
        <h1 id = "title">Department: {{currentDepartment.name}}</h1>
        <div id = "classes">
            <!--TO DO: DISPLAY CLASSES-->
            <ul class="class-list">
                <li class="class" v-for="element in currentDepartment.classes">
                    <button v-on:click="changeCurrentClass(currentDepartment.name,element.number)">{{element.number}}</button>
                </li>
            </ul>  
        </div>
        <div id = "addClass" v-show = "currentUser != ''">
            <input id = "addingClass" v-model="newClass" placeholder="Add a new class" @keyup.enter="addClass(currentDepartment.name, newClass), clearEdit()">
            <label for="addingClass" class="visuallyhidden">Create new class with this name</label>
            <!--TO DO: ALLOW ONLY USERS OR ADMIN TO ADD CLASSES-->
        </div>
        <div id = "editClasses" v-show = "userIsAdmin">
            <h4>Want to edit or delete?</h4>
            <input id = "editingClass" v-model = "classToEdit" placeholder="Edit this Class">
            <label for="editingClass" class="visuallyhidden">Number of the class to edit</label>
            <input id = "editedNumber" v-model = "newNumber" placeholder="New Number for Class">
            <label for="editedNumber" class="visuallyhidden">New number for class being edited</label>
            <button v-on:click = "editClasses(classToEdit, newNumber, currentDepartment.name), clearEdit()">Edit</button>
            <input id = "deleteClass" v-model="classToDelete" placeholder="Delete this class" @keyup.enter="deleteClass(classToDelete, currentDepartment.name), clearEdit()">
            <label for="deleteClass" class="visuallyhidden">Delete this class</label>
            <!--TO DO: ALLOW ONLY ADMIN TO EDIT CLASSES-->
        </div>
        <button v-on:click = "returnToHome">Return To Home Page</button>
    </div>
</template>
<script>
export default {
    name: "DepartmentPage",
    props: ["addClass", "currentDepartment", "addDepartment", "editClasses", "changeCurrentClass", "currentUser", "userIsAdmin", "returnToHome", "deleteClass"],
    data(){
        return{
            newNumber:'',
            classToEdit:'', 
            newClass:'',
            classToDelete:''
        }
        
    },
    methods: {
        clearEdit: function()
        {
            this.classToEdit = "";
            this.newNumber="";
            this.newClass="";
            this.classToDelete="";
        }
    }
}
</script>
<style>
    #classes button{
        border-radius: 8px;
        font-size: 20px;
        background-color: #0066ff;
        border: 2px solid #0000e6;  
        color: white;
    }
    #editClasses{
        line-height: .05cm;
        text-align: center;
        margin-left: 70%;
    }
</style>