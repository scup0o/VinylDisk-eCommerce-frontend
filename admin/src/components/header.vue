

<script>
import "@/assets/css/base.css"
import "@/assets/css/header.css"
import userService from "@/services/user.service.js"
import genreService from "@/services/genre.service.js"
import ArtistServicce from "@/services/artist.service"
import VueJwtDecode from 'vue-jwt-decode';


export default {
    
    components:{
    },

    data() {
            return {
                genres: [],
                artists: [],
                searchText: '',
                numEs: [1,2,3,4,5,6],
                mem:0,
                checkUser: localStorage.getItem('auth'),
                data: null
            }
    },

    watch:{
    },

    computed: {
    },
    
    mounted(){
        /*if (localStorage.tempcart){
            this.cartNumber = JSON.parse((localStorage.tempcart)).totalQuantity;
        }*/

        try{
        if (this.checkUser)
            this.checkUser = VueJwtDecode.decode(localStorage.getItem('auth'))
        //console.log(checkUser);
        this.refreshList();}
        catch(error){
            console.log(error);
        }
    },

    methods: {
        

        logout(){
            userService.logout();
            localStorage.removeItem('auth');
            
            location.reload();
        },

        refreshList(){
        },
    },
};
</script>

<template>
     <nav class="navbar navbar-expand navbar-dark">
        <div class="container-fluid">
            <div class="container">
                <div class="row" style="padding:10px" >
                    
                    <div class="col-sm d-flex justify-content-start" style="margin: auto;">
                        <div style="display: inline-flex;">
                            <img src="../assets/img/logo-vinyldisk.png" class="logo-img">
                            <div style="width: 10px; margin-left: 10px; color:white; font-family: Lobster; font-size: 20px;">
                                <div>ĐĨA THAN</div>
                            </div>
                        </div>
                    </div>

                    
                    <div class="col-sm-5 d-flex justify-content-center"  style="margin: auto;">
                        
                    </div>

                    
                    <div class="col-sm text-end d-flex justify-content-end" style="margin: auto;">
                        <div v-if="!checkUser" class="center-1" id="header-button header-icon">
                                    
                            <button 
                                type="button" 
                                class="btn btn-outline-dark login-button"
                                @click="this.$router.push({ name: 'user.login' })"
                                style="margin-left:10px">Đăng nhập</button>
                        </div>
                        <div v-else>
                            
                            <div id="header-icon" style="display: inline;">
                                <i class="fa-solid fa-gears"
                                    title="Quản lý"
                                    @click="this.$router.push({ name: 'administration'})"></i>
                                <i class="fa-solid fa-user"
                                title="Tài khoản"
                                @click="this.$router.push({name:'account'})"
                                    ></i>
                            </div>
                            <button 
                                type="button" 
                                class="btn signout-button"
                                @click="(logout)">Đăng xuất</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </nav>

    <div class="" style=" background-color: var(--third-color);">
            <div class="container d-flex justify-content-center">
                <div style="background-color:var(--third-color);">
                    <div id="menu" class="">
                        <p></p>
                    </div>
                </div>
            </div>
        </div>
    
    
</template>