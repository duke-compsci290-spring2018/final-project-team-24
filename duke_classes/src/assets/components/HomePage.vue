<template>
    <div id = "homePage">
        <h1>Home Page</h1>
        <ul id = "departments" v-for = "element in departments">
            <button v-on:click = "changeCurrentDepartment(element)">{{element.name}}</button>
            <!--TO DO: DISPLAY DEPARTMENTS-->
        </ul>
        <div id = "addDepartment">
            <input v-model = "newDepartment" placeholder="Add a new Department" @keyup.enter= "addDepartment(newDepartment)">
            <!--TO DO: ALLOW ONLY USERS OR ADMIN TO ADD DEPARTMENTS-->
        </div>
        <div id = "editDepartment">
            <!--TO DO: ALLOW ONLY ADMIN TO EDIT DEPARTMENTS-->
            <input v-model = "departmentToEdit" placeholder="Edit this Department">
            <input v-model = "newName" placeholder="New Name for Department">
            <button v-on:click = "editDepartment(departmentToEdit, newName), clearEdit()">Edit</button>
        </div>
    </div>
</template>
<script>
export default {
    name: "HomePage",
    props: ["departments", "addDepartment", "changeCurrentDepartment", "editDepartment"],
    data () {
            return {
                newDepartment: '',
                departmentToEdit: '',
                newName: ''
            }
    },
    methods: {
        clearEdit: function()
        {
            this.departmentToEdit = "";
            this.newName = ""
        }
    }
}
</script>
<style>
</style>