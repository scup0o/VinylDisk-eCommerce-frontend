<template>
    <div class="container-fluid">
        <div class="row" style="padding-bottom: 20px;">
            <div class="col-5">
                <span>Danh sách mã giảm giá</span>
            </div>
        </div>
        <div class="row">
            <div class="col">
                <button class="btn btn-outline-secondary" 
                        @click="createForm = true"
                        data-aos="fade-up"
                        style="margin-right:10px">
                    Thêm mã giảm giá
                    <i class="fa-solid fa-square-plus"
                        id="util-icon"
                       ></i>
                </button>
                <button class = "btn btn-outline-secondary" 
                        data-aos="fade-up" data-aos-duration="500"
                        @click="deleteAll()">
                    Xóa tất cả
                   <i class="fa-solid fa-trash" id="util-icon"></i> 
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
            <DiscountCodeForm v-if="createForm === true" 
                        @close="createForm = false"
                        @refresh="retrieveDiscountCode()"
                        :e="false"
                        :discountCode="{
                            name: null,
                            value:0
                        }">

            </DiscountCodeForm>
        </div>
        <div class="row" style="padding-top:20px" >
            <div v-for="(discountCode, index) in sliceDiscountCode" :key="index" v-if="filteredDiscountCodesCount > 0">
                <AdminDiscountCodeRender v-if=" index===onPage"
                                    :discountCodes="discountCode"
                                    :is="discountCode"
                                    @refresh="retrieveDiscountCode()">
                </AdminDiscountCodeRender>
                
            </div>
            <p v-else>Không có mã giảm giá nào.</p>
        </div>
        <div class="row text-center d-flex" v-if="filteredDiscountCodesCount > 0">
            <div >
            <button v-if="onPage!=0" 
                    @click="onPage--, onPageTemp--" 
                    class="btn btn-info" 
                    style=" margin-right:10px; background-color: var(--third-color); border-color: black" 
                    title="Trang trước"><i class="fa-solid fa-caret-left"></i></button>
            <div style="display:inline"><input class="form-control" style="width:50px; display: inline;" type="number" v-model="onPageTemp" @keyup.enter="onPage=(onPageTemp-1), onPageTemp=(onPage+1)">/ {{ pageNumber }}</div>
            <button v-if="onPageTemp <pageNumber" 
                    @click="onPage++, onPageTemp=(onPage+1)" 
                    class="btn btn-info" 
                    style=" margin-left:10px; background-color: var(--third-color); border-color: black" 
                    title="Trang sau"><i class="fa-solid fa-caret-right"></i></button>
            </div>
        </div>
    </div>
</template>
<script>
    import '@/assets/css/base.css'
    import DiscountCodeForm from "@/components/DiscountCodeForm.vue"
    import DiscountCodeService from "@/services/discountCode.service"
    import AdminDiscountCodeRender from "@/components/AdminDiscountCodeRender.vue"

    export default{
        components:{
            DiscountCodeForm,
            AdminDiscountCodeRender,
        },

        

        data(){
            return{
                discountCodes: [],
                discountCodesList: [],
                createForm: false,
                searchText: '',
                onPage: 0,
                pageNumber: 0,
                onPageTemp: 1,
            }
        },

        mounted(){
            this.retrieveDiscountCode();
        },

        computed:{
            discountCodeStrings(){
                return this.discountCodes.map((discountCode) =>{
                    const {name} = discountCode;
                    return [name].join("");
                });
            },

            filteredDiscountCodes(){
                if (!this.searchText) return this.discountCodes;
                return this.discountCodes.filter((_discountCode, index) => this.discountCodeStrings[index].toLowerCase().includes(this.searchText));
                
            },

            filteredDiscountCodesCount(){
                //console.log(this.filteredDiscountCodes.length);
                return this.filteredDiscountCodes.length
            },

            sliceDiscountCode(){
                let discountCodeListNumber = Math.ceil(this.filteredDiscountCodes.length / 8);
                this.pageNumber = discountCodeListNumber
                let count = 0;
                let tempDiscountCodes = []
                let i;
                for (i=0; i<discountCodeListNumber; i++){
                    tempDiscountCodes[i] = this.filteredDiscountCodes.slice(count,count+8);
                    count=count+8;
                }
                this.discountCodesList = tempDiscountCodes;
                return tempDiscountCodes;

            }
        },

        methods:{
            async deleteAll(){
                if (confirm("Bạn muốn xóa tất cả Mã giảm giá?")){
                    try{
                        const deleteCount = await DiscountCodeService.deleteAll()
                        if (deleteCount.deletedCount!=0){
                            this.$toast.open({
                                    message: `Xóa ${deleteCount.deletedCount} mã giảm giá thành công`,
                                    type: "success",
                                    duration: 3000 ,
                                    dismissible: true
                                })
                            this.retrieveDiscountCode()
                        }
                        
                    }
                    catch(error){
                        console.log(error)
                    }
                }
            },
            
            async retrieveDiscountCode(){
                try{
                    this.discountCodes = await DiscountCodeService.getAll();
                    console.log(this.discountCodes)
                }
                catch(error){
                    console.log(error);
                }
            }   
        },
    }
</script>
<style scoped>
span{
    font-family: 'RalewayBold';
    font-size: 25px;
}
</style>