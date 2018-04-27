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
            <input v-model = "newDepartment" placeholder="Add a new Department" @keyup.enter= "addDepartment(newDepartment)">  
        </div>
        <div id = "editOrRemoveDepartment" v-show = "userIsAdmin">
            <!--TO DO: ALLOW ONLY ADMIN TO EDIT DEPARTMENTS-->
            <h4>Want to edit or delete?</h4>
            <p><input v-model = "departmentToEdit" placeholder="Edit this Department">
            <input v-model = "newName" placeholder="New Name for Department">
            <button v-on:click = "editDepartment(departmentToEdit, newName), clearEdit()">Edit</button></p>
            <p><input @keyup.enter="deleteDepartment(departmentToDelete)" v-model = "departmentToDelete" placeholder="Delete this Department"></p>
        </div>
    </div>
</template>
<script>
export default {
    name: "HomePage",
    props: ["departments", "addDepartment", "changeCurrentDepartment", "editDepartment", "currentUser", "userIsAdmin", "deleteDepartment"],
    data () {
            return {
                newDepartment: '',
                departmentToEdit: '',
                newName: '',
                departmentToDelete:''
            }
    },
    methods: {
        clearDepartment: function(){
           this.newDepartment = ""; 
        },
        clearEdit: function()
        {
            this.departmentToEdit = "";
            this.newName = "";
            this.newDepartment="";
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