<script>
import * as yup from "yup";
import { Form, Field, ErrorMessage } from "vee-validate";

import UserService from "@/services/user.service.js";
import StaffService from "@/services/staff.service.js";
import OrderService from "@/services/order.service.js";
import VueJwtDecode from "vue-jwt-decode";

import ChangePasswordForm from "@/components/ChangePasswordForm.vue";
import ImgService from "@/services/img.service";


export default {
  components: {
    ChangePasswordForm,
    Form,
    Field,
    ErrorMessage
  },

  data() {
    const FormSchema = yup.object({
        name: yup
                    .string()
                    .required("Tên phải có giá trị.")
                    .min(2, "Tên phải ít nhất 2 ký tự.")
                    .max(50, "Tên có nhiều nhất 50 ký tự."),
                username: yup
                    .string()
                    .required('Tên đăng nhập không được để trống'),
                address: yup.string().required("Địa chỉ không được để trống").max(100, "Địa chỉ tối đa 100 ký tự."),
                phone: yup
                    .string()
                    .required("Số điện thoại không được để trống")
                    .min(10,"Số điện thoại không hợp lệ.")
                    .matches(/^[+]*[(]{0,1}[0-9]{1,4}[)]{0,1}[-\s\./0-9]*$/, 'Số điện thoại không hợp lệ'),
        });

    return {
      account: VueJwtDecode.decode(localStorage.getItem("auth")),
      activetab: "information",
      activeform: "",
      orders: "",
      onPage: 0,
      pageNumber: 0,
      onPageTemp: 1,
      img: true,
      haveData: true,
      img: null,
      images: [],
      imagesPreview: [],
      FormSchema,
    first: true,
    usernameMessage: '',
    };
  },

  mounted() {
    this.getUser();
  },

  methods: {
    async getUser() {
        if (this.first===true){
      if (this.account.role)
        this.account = await StaffService.get(this.account.id);
      else {
        this.account = await UserService.get(this.account.id);
        this.orders = await OrderService.find(this.account._id);
        console.log(this.orders);
      }
      this.first=false
    }
    else{
        if (this.account.role)
        this.account = await StaffService.get(this.account._id);
      else {
        this.account = await UserService.get(this.account._id);
        this.orders = await OrderService.find(this.account._id);
        console.log(this.orders);
      }
    }
    },

    onFileChange(e) {
        this.haveData=false
            console.log(e.target.files);
            this.img=e.target.files
            console.log(this.img)
            let temp = this.images.length
            var selectedFiles = e.target.files;
                
                for (let i=0; i<selectedFiles.length; i++)
                {
                    let reader = new FileReader(); //instantiate a new file reader
                    reader.onload = (e) =>{
                        this.imagesPreview[0] = e.target.result
                    }
                    this.images[0] = selectedFiles[i]
                    reader.readAsDataURL(selectedFiles[i]);
                    console.log(this.images);
	            }

        },

    async edit(data){
        this.usernameMessage=''
        if (this.images[0]) data.img = this.images[0].name;
        const check = await UserService.update(this.account._id,data);
        if (check==='username'){
            this.usernameMessage = 'Tên đăng nhập đã được sử dụng bởi tài khoản khác'
        }
        if (check==='success'){
            this.$toast.open({
                            message: "Chỉnh sửa thông tin thành công",
                            type: "success",
                            duration: 3000 ,
                            dismissible: true
                        })
            this.getUser();
            this.activetab='information'
        }
    },

    check(data){
        if (this.activetab==='edit')
            if(confirm('Thông tin chưa được lưu, bạn có chắc muốn thoát?')){
                this.getUser();
                this.activetab=data;
            }
            else{
                this.activetab='edit'
            }
        else{
            this.activetab=data
        }
    }
  },

  watch:{
    account(){
        this.usernameMessage=''
    }
  },

  computed: {
    filteredOrders() {
      return this.orders;
    },

    filteredOrdersCount() {
      return this.filteredOrders.length;
    },

    sliceOrder() {
      //let pL = this.filteredOrders;
      let orderListNumber = Math.ceil(this.filteredOrders.length / 7);
      this.pageNumber = orderListNumber;
      console.log(orderListNumber);
      let count = 0;
      let tempOrders = [];
      let i;
      for (i = 0; i < orderListNumber; i++) {
        tempOrders[i] = this.filteredOrders.slice(count, count + 7);
        count = count + 7;
        console.log(tempOrders);
      }
      return tempOrders;
    },
  },
};
</script>
<template>
  <div class="container-fluid">
    <div class="row" style="">
      <div class="col-3" style="background-color:var(--main-color); padding-top:200px;">
        <button
          class="form-control btn"
          :class="{btngrad: activetab==='information'}"
          style="padding:10px; color:white"
          @click="check('information')"
        >
          Thông tin tài khoản
        </button>
        <button
          class="form-control btn"
          :class="{btngrad: activetab==='changepass'}"
          style="padding:10px; color:white"
          @click="check('changepass')"
        >
          Đổi mật khẩu
        </button>
      </div>
      <div class="col" >
        <div v-if="activetab === 'information'">
          <img
            :src="`../../src/assets/img/${account.img}`"
            style="
              border-radius: 100%;
              width: 150px;
              height: 150px;
              position: absolute;
              margin-top: 20px;
              margin-left: 50px;
              border: 1px solid black;
            "
          />
          <div
            class="row"
            style="
              padding: 40px;
              padding-top: 105px;
              background-color: rgb(241, 241, 241);
            "
          >
            <div class="box">
              <div class="row">
                <div
                  class="col"
                  style="
                    font-family: 'RalewayBold';
                    font-size: 25px;
                    margin-left: 190px;
                  "
                >
                  {{ account.name }}
                </div>
                <div class="col text-end" v-if="(!account.role)">
                  <button class="btn btn-dark" @click="activetab = 'edit'">
                    Chỉnh sửa
                    <i
                      class="fa-regular fa-pen-to-square"
                      style="margin-left: 5px"
                    ></i>
                  </button>
                </div>
              </div>
              <div
                class="row"
                style="
                  padding: 70px;
                  font-family: 'RalewayBold';
                  font-size: 20px;
                  padding-top: 60px;
                  padding-bottom: 30px;
                "
              >
                Thông tin cá nhân
              </div>
              <div>
                
                <div
                  class="row"
                  style="margin: 100px; margin-top:0px; padding-bottom: 10px; margin-bottom: 0; border-bottom:1px solid rgb(186, 186, 186)"
                  
                >
                  <div class="col-3">Mã nhân viên:</div>
                  <div class="col">
                    {{ account.staffId }}
                  </div>
                </div>
                <div
                  class="row"
                  style="margin: 100px; margin-top:0px; padding-bottom: 10px; margin-bottom: 0; padding-top:20px; border-bottom:1px solid rgb(186, 186, 186)"
                >
                  <div class="col-3">Họ và tên:</div>
                  <div class="col">
                    {{ account.name }}
                  </div>
                </div>
                <div
                  class="row"
                  style="margin: 100px; margin-top:0px; padding-bottom: 10px; margin-bottom: 0; padding-top:20px; border-bottom:1px solid rgb(186, 186, 186)"
                  v-if="account.role"
                >
                  <div class="col-3">Chức vụ:</div>
                  <div class="col">
                    <div v-if="account.role==='employee'">Nhân viên</div>
                    <div v-else>Quản lý</div>
                  </div>
                </div>
                
                <div
                  class="row"
                  style="margin: 100px; margin-top:0px; padding-bottom: 10px; margin-bottom: 0; padding-top:20px; border-bottom:1px solid rgb(186, 186, 186)"
                >
                  <div class="col-3">Điện thoại:</div>
                  <div class="col">
                    {{ account.phone }}
                  </div>
                </div>
                
                <div
                  class="row"
                  style="margin: 100px; margin-top:0px; padding-bottom: 10px; margin-bottom: 0; padding-top:20px; border-bottom:1px solid rgb(186, 186, 186)"
                  
                >
                  <div class="col-3">Địa chỉ:</div>
                  <div class="col">
                    {{ account.address }}
                  </div>
                </div>
              </div>
            </div>
          </div>
    </div>
      </div>
    </div>
  </div>
  <ChangePasswordForm
    v-if="activetab === 'changepass'"
    :account="account"
    @close="activetab = 'information'"
  ></ChangePasswordForm>
  <DeleteAccountForm
    v-if="activetab === 'deleteaccount'"
    :account="account"
    @close="activetab = 'information'"
  ></DeleteAccountForm>
</template>
<style scoped>
.box {
  box-shadow: 0 2px 8px rgba(132, 132, 132, 0.33);
  padding: 10px;
  border-radius: 10px 10px 10px 10px;
  background-color: rgb(255, 255, 255);
}

.active{
    background-color: rgb(228, 229, 239);
    width: 400px;
    margin-left: -20px;
    border:none;
}

         
         
         
.btngrad {
            background-image: linear-gradient(to right, #ffffff 0%,   var(--main-color) 51%, var(--main-color)  100%);
            margin: 10px;
            padding: 15px 45px;
            text-align: center;
            transition: 0.5s;
            background-size: 200% auto;
            color: white;            
            border-radius: 10px;
            display: block;
          }

          .btngrad:hover {
            background-position: right center; /* change the direction of the change here */
            color: #fff;
            text-decoration: none;
          }
         
         
.hover-e:hover {
  opacity: 70%;
}
</style>
