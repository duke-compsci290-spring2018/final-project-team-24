<template>
    <div id = "adminRequests">
        <h1 id = "title">Administrator Requests</h1>
        <div id = "requests">
            <!--TO DO: DISPLAY ADMIN REQUESTS AND ALLOW FOR APPROVAL-->
            <ul  v-for = "user in users" v-show = "user.submittedAdminRequest">
                <li><h3 >{{user.email}}</h3></li>
                <li><button v-on:click = "grantAdmin(user.email)">Grant Administrator Status</button></li>
            </ul>
        </div> 
        <button v-on:click = "returnToHome">Return To Home Page</button>
    </div>  
</template>
<script>
export default {
    name: "AdminRequests",
    props: ["users", "grantAdmin", "returnToHome"]
}
</script>
<style>
    #requests{
/*        border: 2px solid #0000e6;*/
        line-height: .05cm;
    }
</style>