<template>
    <div id = "departmentPage">
        <h1>Department Name</h1>
        <div id = "classes">
            <!--TO DO: DISPLAY CLASSES-->
            <ul class="class-list">
                <li class="class" v-for="element in currentDepartment.classes">
                    <button v-on:click="changeCurrentClass(element.number), print(element)">{{element.number}}</button>
                </li>
            
            </ul>
            
        </div>
        <div id = "addDepartment">
            <input v-model="newDepartment" placeholder="Add a new department" @keyup.enter="addDepartment(newDepartment)">
            <!--TO DO: ALLOW ONLY USERS OR ADMIN TO ADD CLASSES-->
        </div>
        <div id = "editClasses">
            <!--TO DO: ALLOW ONLY ADMIN TO EDIT CLASSES-->
        </div>
    </div>
</template>
<script>
export default {
    name: "DepartmentPage",
    props: ["addClass", "currentDepartment", "changeCurrentClass", "addDepartment"],
    data(){
        return{
            newDepartment:''
        }
        
    },
    methods: {
        print: function(thing)
        {
            console.log(thing);
        }
    }
}
</script>
<style>
</style>