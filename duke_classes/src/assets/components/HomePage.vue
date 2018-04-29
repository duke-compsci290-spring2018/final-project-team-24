<template>
    <div id = "homePage">
        <h1 id = "title">Home</h1>
        <ul id = "departments">
            <!--TO DO: DISPLAY DEPARTMENTS-->
            <li v-for = "element in departments">
                <button v-on:click = "changeCurrentDepartment(element)">{{element.name}}</button>
            </li>
        </ul>
        <div id = "addDepartment" v-show = "currentUser != ''">
            <!--TO DO: ALLOW ONLY USERS OR ADMIN TO ADD DEPARTMENTS-->
            <input id = "newDep" v-model = "newDepartment" placeholder="Add a new Department" @keyup.enter= "addDepartment(newDepartment), clearEdit()"> 
            <label for="newDep" class="visuallyhidden">Create new department with this name</label>
        </div>
        <div id = "editOrRemoveDepartment" v-show = "userIsAdmin">
            <!--TO DO: ALLOW ONLY ADMIN TO EDIT DEPARTMENTS-->
            <h4>Want to edit or delete?</h4>
            <p>
                <input id = "editingDepartment" v-model = "departmentToEdit" placeholder="Edit this Department">
                <label for="editingDepartment" class="visuallyhidden">Name of the department to edit</label>
                <input id = "editedName" v-model = "newName" placeholder="New Name for Department">
                <label for="editedName" class="visuallyhidden">New name for department being edited</label>
                <button v-on:click = "editDepartment(departmentToEdit, newName), clearEdit()">Edit</button>
            </p>
            <p>
                <input id = "deleteDep" @keyup.enter="deleteDepartment(departmentToDelete),clearEdit()" v-model = "departmentToDelete" placeholder="Delete this Department">
                <label for="deleteDep" class="visuallyhidden">Delete this department</label>
            </p>
        </div>
        <h4 v-show="checkFieldsHP">Please fill out all fields</h4>
    </div>
</template>
<script>
export default {
    name: "HomePage",
    props: ["departments", "addDepartment", "changeCurrentDepartment", "editDepartment", "currentUser", "userIsAdmin", "deleteDepartment", "checkFieldsHP"],
    data () {
            return {
                newDepartment: '',
                departmentToEdit: '',
                newName: '',
                departmentToDelete:'',
                fieldsNotFilledOut: false
            }
    },
    methods: {
        clearEdit: function()
        {
            this.departmentToEdit = "";
            this.newName = "";
            this.newDepartment="";
            this.departmentToDelete = "";
        }
    }
}
</script>
<style>
    #departments button{
        border-radius: 8px;
        font-size: 20px;
        background-color: #0066ff;
        border: 2px solid #0000e6;  
        color: white;
    }
    #editOrRemoveDepartment{
        line-height: .05cm;
        text-align: center;
        margin-left: 70%;
    }
</style>