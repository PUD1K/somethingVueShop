<template>
    <div class="page-sign-up">
        <div class="columns">
            <div class="column is-4 is-offset-4">
                <h1 class="title">Login</h1>

                <form @submit.prevent="submitForm">
                    <div class="field">
                        <label>Username</label>
                        <div class="control">
                            <input type="text" class="input" v-model="username">
                        </div>
                    </div>

                    <div class="field">
                        <label>Password</label>
                        <div class="control">
                            <input type="password" class="input" v-model="password">
                        </div>
                    </div>

                    <div class="notification is-danger" v-if="errors.length">
                        <p v-for="error in errors" v-bind:key="error">{{error}}</p> 
                    </div>

                    <div class="field">
                        <div class="control">
                            <button class="button is-dark">Login</button>
                        </div>
                    </div>

                    <hr>

                    Or <router-link to="/sign-up">click here</router-link> to sign up.   
                </form>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
export default {
    name: 'LogIn',
    data(){
        return{
            username: '',
            password: '',
            errors: []
        }
    },
    mounted(){
        document.title = "Login | Thing"
    },
    methods:{
        async submitForm(){
            axios.defaults.headers.common["Authorization"] = ""
            console.log(this.errors)

            localStorage.removeItem("token")

            const formData = {
                username: this.username,
                password: this.password
            }

            await axios
                .post("/api/v1/token/login/", formData)
                .then(response => {
                    //спарсили токен с запроса
                    const token = response.data.auth_token
                    //поместили токен в store
                    this.$store.commit("setToken", token)
                    //добавили токен в глобалку axios
                    axios.defaults.headers.common["Authorization"] = "Token " + token
                    //добавили токен в localStorage
                    localStorage.setItem("token", token)
                    localStorage.setItem("username", formData.username)
                    
                    const toPath = this.$route.query.to || '/cart'

                    this.$router.push(toPath)
                })
                .catch(error => {
                        // if (error.response){
                        //     for(const property in error.response.data){
                        //         this.errors.push(`${property}: ${error.response.data[property]}`)
                        //     }
                        
                        //     console.log(JSON.stringify(error.response.data))
                        // }else if(error.message){
                        //     this.errors.push('Something weit wrong. Please try again.')

                        //     console.log(JSON.stringify(error))
                        // }
                        console.log(error)
                })
        }
    }
}
</script>
