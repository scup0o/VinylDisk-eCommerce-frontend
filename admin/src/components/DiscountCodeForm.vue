<template>
    <div class="modal-mask">
        <div class="modal-wrapper">
            <div class="modal-container">
                <div class="modal-header">
                    <label class="" style="margin: auto; font-size: 20px;" v-if="edit===false">Thêm mã giảm giá</label>
                    <label class="" style="margin: auto; font-size: 20px;" v-else>Chỉnh sửa mã giảm giá</label>

                </div>
                    <Form @submit="createDiscountCode" :validation-schema="FormSchema">
                        <div class="modal-body">
                        <div class="row">
                            <div class="form-group">
                                <label for="name">Tên mã giảm giá: </label>
                                <Field name="name" type="text" class="form-control" v-model="discountCodeNew.name">
                                </Field>
                                <ErrorMessage name="name" style="color:red"></ErrorMessage>
                                <p style="color:red">{{ nameMessage }}</p>
                            </div>
                        </div>
                        <div class="row">
                            <div class="col-6">
                                <div class="form-group">
                                    <label for="value">Giá trị giảm giá: </label>
                                    <Field name="value" type="number" class="form-control"
                                        v-model="discountCodeNew.value">
                                    </Field>
                                    <ErrorMessage name="value" class="error-feedback"></ErrorMessage>
                                    <p style="color:red">{{ valueMessage }}</p>
                                </div>
                            </div>
                            <div class="col-6">
                                <div class="form-group">
                                    <label for="type">Đơn vị:</label>
                                    <div style="margin:auto">
                                        <Field name="type" type="radio" value='%' v-model="type" style="margin-left: 30px">
                                        </Field> %
                                        <Field name="type" type="radio" value='VND' v-model="type" style="margin-left: 30px">
                                        </Field> VND
                                    </div>
                                    <p></p>
                                </div>
                            </div>  
                        </div>
                        <div class="row">
                            <div class="form-group">
                                <label for="description">Mô tả:</label>
                                        <Field style="height:100px" as="textarea" name="description" type="text" class="form-control" v-model="discountCodeNew.description">
                                        </Field>
                                        <ErrorMessage name="description" style="color:red"></ErrorMessage>
                                    </div>
                        </div>
                        <div class="row">
                            <div class="col-6">
                                <div class="form-group">
                                <label for="startDate">Ngày có hiệu lực:</label>
                                        <Field name="startDate" type="date" class="form-control" v-model="discountCodeNew.startDate">
                                        </Field>
                                        <ErrorMessage name="startDate" style="color:red"></ErrorMessage>
                                    </div>
                            </div>
                            <div class="col-6">
                                <div class="form-group">
                                <label for="expirationDate">Ngày hết hiệu lực:</label>
                                        <Field name="expirationDate" type="date" class="form-control" v-model="discountCodeNew.expirationDate">
                                        </Field>
                                        <ErrorMessage name="expirationDate" style="color:red"></ErrorMessage>
                                    </div>
                            </div>
                            <div style="padding-left:10px; color: red">{{ datemessage }}</div>
                        </div>
                        <div class="row" style="padding-top:15px">
                            <div style="margin:auto; display: inline;">
                            <label>Hiệu lực:</label><Field name="active" type="radio" :value="true" v-model="discountCodeNew.active" style="margin-left:30px">
                                        </Field> Có
                                        <Field name="active" type="radio" :value='false' v-model="discountCodeNew.active" style="margin-left:30px">
                                        </Field> Không
                                    </div>
                        </div>
                    </div>
                    <div class="text-center">
                        <button v-if="edit===false" class="btn btn-info form-button" style="margin-right: 10px">
                            Thêm mã giảm giá
                        </button>
                        <button v-else class="btn btn-info form-button" style="margin-right: 10px">
                            Lưu mã giảm giá
                        </button>
                        <button @click="$emit('close'), $emit('refresh')"
                            class="btn btn-info form-button"
                            style="width: 150px;">
                            Hủy
                        </button>
                    </div>
                    </Form>
            </div>
        </div>
    </div>
</template>
<script>
import "@/assets/css/base.css"
import * as yup from "yup";
import { Form, Field, ErrorMessage } from "vee-validate";
import ImgService from "@/services/img.service";
import DiscountCodeService from "@/services/discountCode.service"
import moment from 'moment';

export default {
    components: {
        Form,
        Field,
        ErrorMessage,
    },

    props: {
            discountCode: {type: Object, required: true},
            e: {type: Boolean, required: true}
        },

    data() {

        const FormSchema = yup.object({
            name: yup
                .string()
                .required("Tên không được để trống")
                .min(2, "Tên phải ít nhất 2 ký tự.")
                .max(50, "Tên có nhiều nhất 50 ký tự."),
            value: yup
                .number()
                .required("Giá trị không được để trống")
                .typeError("Giá trị phải là số")
                .min(1, "Giá trị phải lớn hơn 1")
        });

        return {
            edit: this.e,
            discountCodeNew: this.discountCode,
            expirationDate: new Date(),
            FormSchema,
            img: null,
            images: [],
            nameMessage:'',
            haveData: true,
            type: '%',
            valueMessage: '',
            datemessage: '',
        }
    },

    mounted() {
        if (this.discountCodeNew.value > 100) this.type='VND'
        if (this.discountCodeNew.active === undefined || this.discountCodeNew.active === null || this.discountCodeNew.active === "") this.discountCodeNew.active = true;
        //this.expirationDate=new Date('2006-03-05T00:00:00')
        this.discountCodeNew.startDate=this.getDate(this.discountCodeNew.startDate).toString();
        this.discountCodeNew.expirationDate=this.getDate(this.discountCodeNew.expirationDate).toString();
        console.log(this.discountCodeNew.startDate);
        console.log(this.expirationDate)
    },

    methods: {
        getDate(date) {
                return moment(date, 'YYYY-MM-DD').format('YYYY-MM-DD');
            },

        checkType(){
            if (this.discountCodeNew.value > 100) this.type = 'VND'
        },

        async createDiscountCode(data) {
            try {
                console.log(this.type)
                console.log(data)
                if (data.startDate >= data.expirationDate) this.datemessage = 'Ngày hết hiệu lực không được < ngày bắt đầu hiệu lực'
                else{
                    if((this.type === '%' && this.discountCodeNew.value > 100)){
                        this.valueMessage = 'Giá trị không được > 100%'
                    }
                    else{
                        if ((this.type === 'VND' && this.discountCodeNew.value <999)){
                            this.valueMessage = 'Giá trị không được < 999VND'
                        }
                        else{
                            if (this.edit === false){
                                if (data.description === undefined || data.description === null || data.description === "") data.description = 'Không có mô tả'
                                const check = await DiscountCodeService.create(data);
                                if (check === false){
                                    this.nameMessage = 'Tên mã giảm giá đã tồn tại'
                                }else{
                                    this.$toast.open({
                                        message: "Thêm mã giảm giá thành công",
                                        type: "success",
                                        duration: 3000 ,
                                        dismissible: true
                                    })
                                    this.$emit('close');
                                    this.$emit('refresh');
                                }
                            }
                            else{
                                const check = await DiscountCodeService.update(this.discountCodeNew._id, data);
                                if (check === false){
                                    this.nameMessage = 'Tên mã giảm giá đã tồn tại'
                                }
                                else{
                                    const headers = { 'Content-Type': 'multipart/form-data' };
                                        if (this.images[0] !== undefined) await ImgService.upload(this.img, { headers });
                                        this.$toast.open({
                                            message: "Chỉnh sửa mã giảm giá thành công",
                                            type: "success",
                                            duration: 3000 ,
                                            dismissible: true
                                        })
                                    this.$emit('close');
                                    this.$emit('refresh');
                                }
                            }
                        }
                    }
                }
                
            }
            catch (error) {
                console.log(error);
            }
        },
        
    }
}
</script>
<style scoped>
label{
    padding-top:5px;
    padding-bottom:5px;
    font-family: RalewayBold;
}

.form-button{
    background-color: var(--secondary-color);
    color:white;
    border-color: var(--secondary-color);
}
.form-control{
    border-width: 1.55px;
    border-color: var(--secondary-color);
    box-shadow: 0.5px 0.5px 7px 0.5px rgb(226, 227, 232);
}
.error-feedback{
    color:red;
}
.preview{
		  display: flex;
		  justify-content: center;
		  align-items: center;
		  height: 100px;
		  width: 100px;}
.modal-mask {
    position: fixed;
    z-index: 9999;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: table;
    transition: opacity 0.3s ease;

}

.modal-wrapper {
    display: table-cell;
    vertical-align: middle;
}

.modal-container {
    width: 600px;
    margin: 0px auto;
    padding: 20px 20px;
    background-color: #fff;
    border-radius: 2px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
    transition: all 0.3s ease;
    
    /*background-color: rgb(196, 219, 219);*/
    
}
.modal-header h3 {
    margin-top: 0;
}

.modal-body {
    margin: 20px 0;
    overflow-y: scroll;
    overflow-x: hidden;
    height:500px;
    background-color: white;
    padding:20px
}
</style>