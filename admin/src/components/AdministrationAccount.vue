<script>
    import AdminAccountRender from '@/components/AdminAccountRender.vue';

    import UserService from '@/services/user.service.js'
    import StaffService from '@/services/staff.service.js'
    import AccountForm from "@/components/AccountForm.vue"


    export default{
        components:{
            AdminAccountRender,
            AccountForm
        },
        props:{
            accountTab:{type: String, required: true}
        },

        watch:{
            accountTab(){
                this.getAccount();
            }
        },

        data(){
            return{
                onPage: 0,
                pageNumber: 0,
                onPageTemp: 1,
                searchText:'',
                accountlist:[],
                edit:false,
            }
        },

        mounted(){
            this.getAccount();
            console.log(this.accountTab)
        },

        methods:{
            async getAccount(){
                try{
                    this.onPage= 0;
                    this.pageNumber= 0;
                    this.onPageTemp= 1;
                    this.searchText='';
                    if (this.accountTab === 'user') this.accountlist = await UserService.getAll();
                    if (this.accountTab === 'staff') this.accountlist = await StaffService.getAll();
                    console.log(this.accountlist)}
                    
                catch(error){
                    console.log(error)
                }
            },
            async getAccount2(){
                try{
                    if (this.accountTab === 'user') this.accountlist = await UserService.getAll();
                    if (this.accountTab === 'staff') this.accountlist = await StaffService.getAll();
                    console.log(this.accountlist)}
                    
                catch(error){
                    console.log(error)
                }
            }
        },

        computed:{
            filterAccountTab(){return this.accountTab},
            accountStrings(){
                if (this.accountTab === 'user')
                    return this.accountlist.map((account) =>{
                        const {name, username } = account;
                        return [name, username].join("");
                    });
                else{
                    return this.accountlist.map((account) =>{
                        const {name, staffId } = account;
                        return [name, staffId].join("");
                    });
                }
            },

            filteredAccounts(){
                if (!this.searchText) return this.accountlist;
                else{
                    this.onPage = 0;
                    this.onPageTemp = 1;
                    return this.accountlist.filter((_account, index) => this.accountStrings[index].toLowerCase().includes(this.searchText.toLowerCase()));
                    
                }
            },

            filteredAccountsCount(){
                return this.filteredAccounts.length
            },

            sliceAccount(){
                //let pL = this.filteredAccounts;
                let accountListNumber = Math.ceil(this.filteredAccounts.length / 7);
                this.pageNumber = accountListNumber
                console.log(accountListNumber)
                let count = 0;
                let tempAccounts = []
                let i;
                for (i=0; i<accountListNumber; i++){
                    tempAccounts[i] = this.filteredAccounts.slice(count,count+7);
                    count=count+7;
                    console.log(tempAccounts);
                }
                return tempAccounts;

            }
        },
    }
</script>
<template>
    <div class="container-fluid">  
        <div class="row" style="padding-bottom: 20px;">
            <div class="col-5">
                <span>Danh sách tài khoản</span>
            </div>
        </div>
        <div class="row">
            <div class="col">
                <button class="btn btn-outline-secondary" 
                        @click="edit = true"
                        data-aos="fade-up"
                        style="margin-right:10px"
                        v-if="accountTab==='staff'">
                    Tạo tài khoản
                    <i class="fa-solid fa-square-plus"
                        id="util-icon"
                       ></i>
                </button>
                
            </div>
            <div class="col-4">
                <div class="input-group" style="">
                    <input
                            type = "text"
                            class ="form-control search-form"
                            placeholder="Nhập thông tin cần tìm"
                            v-model = "searchText"/>
                    <button 
                            class = "btn btn-outline-secondary search-button"
                            type = "button">
                            <i class="fa-solid fa-magnifying-glass search-icon"></i>
                    </button>
                </div>

            </div>
        </div>
        
        <div class="row" style="padding-top:20px" >
            <div v-for="(accounts, index) in sliceAccount" :key="index" v-if="filteredAccountsCount > 0">
                <AdminAccountRender v-if=" index===onPage"
                                    :accounts="accounts"
                                    :accountTab="filterAccountTab"
                                    :is="accounts"
                                    @refresh="getAccount()"
                                    @refresh2="getAccount2()">
                </AdminAccountRender>
                
            </div>
            <p v-else>Không có tài khoản nào.</p>
        </div>
        <div class="row text-center d-flex" v-if="filteredAccountsCount > 0" style="padding-top:10px">
            <div >
            <button v-if="onPage!=0" 
                    @click="onPage--, onPageTemp--" 
                    class="btn btn-info" 
                    style=" margin-right:10px; background-color: var(--third-color); bAccount-color: black" 
                    title="Trang trước"><i class="fa-solid fa-caret-left"></i></button>
            <div style="display:inline"><input class="form-control" style="width:50px; display: inline;" type="number" v-model="onPageTemp" @keyup.enter="onPage=(onPageTemp-1), onPageTemp=(onPage+1)">/ {{ pageNumber }}</div>
            <button v-if="onPageTemp <pageNumber" 
                    @click="onPage++, onPageTemp=(onPage+1)" 
                    class="btn btn-info" 
                    style=" margin-left:10px; background-color: var(--third-color); bAccount-color: black" 
                    title="Trang sau"><i class="fa-solid fa-caret-right"></i></button>
            </div>
        </div>
    </div>
    <AccountForm :account="{
                            staffId:null,
                            password:null,
                            phone:null,
                            name:null,
                            address:null,
                            role:'employee',
                            img:null
                            }" :e="false" v-if="accountTab==='staff' && edit===true" @close="edit=false" @refresh="getAccount2()"></AccountForm>

</template>
<style scoped>
span{
    font-family: 'RalewayBold';
    font-size: 25px;
}

#util-icon{
    margin-left: 10px;
}

</style>