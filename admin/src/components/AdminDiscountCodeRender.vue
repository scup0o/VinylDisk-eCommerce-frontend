<template>
    <div class="row">
        <div class="col-3" v-for="(discountCode, index) in discountCodes" :key="discountCode" style="padding-bottom:25px" >
            <div class="card" data-aos="fade-up" :data-aos-duration="(index+1)*200">
                <div class="row">
                    <div class="col text-center"><label v-snip="1">Tên mã giảm giá: {{ discountCode.name }}</label></div>
                </div>
                <div class="row" id="description-box">
                    <div class="col text-center"><p style="display: inline;">Giá trị: </p><p v-if="discountCode.value>100" style="display: inline">{{ new Intl.NumberFormat('vi-VN', {
                                                        style: 'currency',
                                                        currency: 'VND'
                                                    }).format(discountCode.value) }}</p>
                                                <p v-else style="display: inline"> {{ discountCode.value }} %</p></div>
                                                <div class="row">
                    <div class="col text-center" v-if="(getDate(discountCode.startDate) != 'Invalid date')">
                        Ngày có hiệu lực: {{ getDate(discountCode.startDate) }}
                    </div>
                </div>
                <div class="row">
                    <div class="col text-center" v-if="(getDate(discountCode.expirationDate) != 'Invalid date')">
                        Ngày hết hiệu lực: {{ getDate(discountCode.expirationDate) }}
                    </div>
                </div>
                <div class="row">
                    <div class="col text-center" v-if="discountCode.active===true">
                        Hiệu lực: Có
                    </div>
                    <div class="col text-center" v-if="discountCode.active===false">
                        Hiệu lực: Không
                    </div>
                </div>
                </div>
                
                <div class="row" id="util-button">
                    <div class="col text-center" style="">
                        <button class="btn btn-outline-secondary" 
                                @click="editDiscountCode = discountCode, edit = true"
                                style="margin-right:10px;">
                            <i class="fa-solid fa-pen-to-square"></i>
                        </button>
                        <button class="btn btn-outline-secondary delete-icon"
                                @click="deleteDiscountCode(discountCode)">
                            <i class="fa-solid fa-trash-can"></i>
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <DiscountCodeForm v-if="edit === true"
                :discountCode="editDiscountCode"
                :e="true"
                @close="edit = false"
                @refresh="this.$emit('refresh')">
    </DiscountCodeForm>
</template>
<script>
    import DiscountCodeForm from "@/components/DiscountCodeForm.vue";
    import DiscountCodeService from "@/services/discountCode.service"
    import moment from 'moment';

    export default{
        components:{
            DiscountCodeForm
        },
        emits: ["refresh"],

        props:{
            discountCodes:{
                type:Array, default: []
            }
        },

        data(){
            return{
                editDiscountCode: null,
                edit: false,
            }
        },

        methods:{
            async deleteDiscountCode(data){
                try{
                    if (confirm(`Bạn có chắc muốn xóa mã giảm giá ${data.name}?`)){
                    const check = await DiscountCodeService.delete(data._id)
                    if (check===false){
                        this.$toast.open({
                                message: "Xóa mã giảm giá thất bại",
                                type: "error",
                                duration: 3000 ,
                                dismissible: true
                            })
                    }
                    else{
                        this.$toast.open({
                            message: "Xóa thành công",
                            type: "success",
                            duration: 3000,
                            dismissible: true
                        })
                        this.$emit('refresh')
                    }}
                }
                catch(error){
                    console.log(error)
                }
            },

            getDate : function (date) {
                return moment(date, 'YYYY-MM-DD').format('DD/MM/YYYY');
            }
        }
    }
</script>

<style scoped>

label{
    font-family: 'RalewayBold';
}
.card{
    background-color: white;
    padding:15px;
    border:none;
    box-shadow: 0 2px 8px rgba(176, 176, 176, 0.33);
}

.card:hover{
    transform: scale(1.05);
    z-index: 999;
}

.card:hover #util-button{
    visibility: visible;
}

.card:hover #description-box{
    height:120px;
}

#description-box{
    height: 100px;
}

#util-button{
    visibility: hidden;
}

</style>