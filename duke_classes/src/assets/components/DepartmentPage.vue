<template>
    <div id = "departmentPage">
        <h1>{{currentDepartment.name}}</h1>
        <div id = "classes">
            <!--TO DO: DISPLAY CLASSES-->
            <ul class="class-list">
                <li class="class" v-for="element in currentDepartment.classes">
                    <button v-on:click="changeCurrentClass(currentDepartment.name,element.number)">{{element.number}}</button>
                </li>
            </ul>  
        </div>
        <div id = "addDepartment" v-show = "currentUser != ''">
            <input v-model="newClass" placeholder="Add a new class" @keyup.enter="addClass(currentDepartment.name, newClass)">
            <!--TO DO: ALLOW ONLY USERS OR ADMIN TO ADD CLASSES-->
        </div>
        <div id = "editClasses" v-show = "userIsAdmin">
            <input v-model = "classToEdit" placeholder="Edit this Class">
            <input v-model = "newNumber" placeholder="New Number for Class">
            <button v-on:click = "editClasses(classToEdit, newNumber, currentDepartment.name), clearEdit()">Edit</button>
            
            <!--TO DO: ALLOW ONLY ADMIN TO EDIT CLASSES-->
        </div>
        <button v-on:click = "returnToHome">Return To Home Page</button>
    </div>
</template>
<script>
export default {
    name: "DepartmentPage",
    props: ["addClass", "currentDepartment", "addDepartment", "editClasses", "changeCurrentClass", "currentUser", "userIsAdmin", "returnToHome"],
    data(){
        return{
            newNumber:'',
            classToEdit:'', 
            newClass:''
        }
        
    },
    methods: {
        clearEdit: function()
        {
            this.classToEdit = "";
            this.newName = "";
            this.newNumber="";
            this.newClass ="";
        }
    }
}
</script>
<style>
</style>