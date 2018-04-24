<template>
    <div id = "adminRequests">
        <h1>Administrator Requests</h1>
        <div id = "requests" v-for = "user in users">
            <!--TO DO: DISPLAY ADMIN REQUESTS AND ALLOW FOR APPROVAL-->
            <h3 v-show = "user.submittedAdminRequest">{{user.email}}</h3>
            <button v-show = "user.submittedAdminRequest" v-on:click = "grantAdmin(user.email)">Grant Administrator Status</button>
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
</style>