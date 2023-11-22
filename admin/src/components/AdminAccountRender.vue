<script>
    import AccountForm from './AccountForm.vue';
    import UserService from '../services/user.service';
    import StaffService from '../services/staff.service';
    export default{
        components:{
            AccountForm
        },  

        props:{
            accounts:{type: Array, required: true},
            accountTab:{type:String}
        },

        data(){
            return{
                editAccount: null,
                edit: false,
            }
        },

        mounted(){
            
        },

        methods:{
            async deleteAccount(data){
                if (confirm('Bạn có chắc muốn xóa tài khoản?')){
                    let check;
                if (this.accountTab==='user') check = await UserService.delete(data._id)
                if (this.accountTab==='staff') check = await StaffService.delete(data._id)
                if (check===true){
                    this.$toast.open({
                                message: "Xóa tài khoản thành công",
                                type: "success",
                                duration: 3000 ,
                                dismissible: true
                            })
                    this.$emit('refresh2')
                }
                
                }
            }
        }
    }
</script>
<template>
        <div v-for="account in accounts" :key="account" style="padding-bottom: 10px;">
            <div class="box row">
                <div class="col-2 d-flex" style="margin: auto;">
                    <img :src="`../../src/assets/img/${account.img}`" style="width: 50px; height: 50px; border-radius: 100%; display: inline;">
                    <p style="display: inline; margin-left:-10px; margin: auto" v-if="accountTab==='user'">{{ account.username }}</p>
                    <p style="display: inline; margin-left:-10px; margin: auto;" v-if="accountTab==='staff'">{{ account.staffId }}</p>
                </div>
                <div class="col-2 d-flex" style="margin: auto;">
                    {{ account.name }}
                </div>
                <div class="col-3" style="margin: auto;" v-if="accountTab==='user'">
                    <div>{{ account.email }}</div>
                    <div style="">{{ account.phone }}</div>
                </div>
                <div class="col-1" style="margin: auto;" v-if="accountTab==='staff'">
                    <div style="">{{ account.phone }}</div>
                </div>
                <div class="col-2 text-center" v-if="accountTab==='staff'" style="margin: auto; text-align: center;">
                    <p style="margin: auto;" v-if="account.role === 'admin'">Quản lý</p>
                    <p style="margin: auto;" v-if="account.role === 'employee'">Nhân viên</p>
                </div>
                <div class="col-3 d-flex" v-snip="{ lines: 10 }" style="margin: auto;">
                    {{ account.address }}
                </div>
                <div class="col text-end" style="margin: auto">
                            <button v-if="accountTab==='staff'" class="btn btn-outline-secondary" 
                                    @click="editAccount = account; edit = true"
                                    style="margin-right:10px">
                                    <i class="fa-solid fa-pen-to-square"></i>
                            </button>
                            <button class="btn btn-outline-secondary delete-icon"
                                    @click="deleteAccount(account)">
                                    <i class="fa-solid fa-trash-can"></i>
                            </button>
                            
                        </div>
            </div>
        </div>
        <AccountForm :account="editAccount" :e="true" v-if="accountTab==='staff' && edit===true" @close="edit=false" @refresh="this.$emit('refresh2')"></AccountForm>
</template>
<style scoped>
.box{
    box-shadow: 0 2px 8px rgba(132, 132, 132, 0.33);
    background-color: white;
    border-radius: 10px 10px 10px 10px;
    padding: 10px;
}

.box:hover{
    transform: scale(1.01);
    transition: transform 100ms ease-in-out;
}
</style>