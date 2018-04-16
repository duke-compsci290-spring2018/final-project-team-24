<template>
    <div id = "departmentPage">
        <h1>Department Name</h1>
        <div id = "classes">
            <!--TO DO: DISPLAY CLASSES-->
            <ul class="class-list">
                <li class="class" v-for="element in currentDepartment.classes">
                    <button v-on:click="changeCurrentClass(currentDepartment.name,element.number), print(element)">{{element.number}}</button>
                </li>
            </ul>  
        </div>
        <div id = "addDepartment">
            <input v-model="newClass" placeholder="Add a new class" @keyup.enter="addClass(currentDepartment.name, newClass)">
            <!--TO DO: ALLOW ONLY USERS OR ADMIN TO ADD CLASSES-->
        </div>
        <div id = "editClasses">
            <input v-model = "classToEdit" placeholder="Edit this Class">
            <input v-model = "newNumber" placeholder="New Number for Class">
            <button v-on:click = "editClasses(classToEdit, newNumber, currentDepartment.name), clearEdit()">Edit</button>
            
            <!--TO DO: ALLOW ONLY ADMIN TO EDIT CLASSES-->
        </div>
    </div>
</template>
<script>
export default {
    name: "DepartmentPage",
    props: ["addClass", "currentDepartment", "addDepartment", "editClasses", "changeCurrentClass"],
    data(){
        return{
            newNumber:'',
            classToEdit:'', 
            newClass:''
        }
        
    },
    methods: {
        print: function(thing)
        {
            console.log(thing);
        },
        clearEdit: function()
        {
            this.classToEdit = "";
            this.newName = "";
            this.newNumber="";
        }
    }
}
</script>
<style>
</style>