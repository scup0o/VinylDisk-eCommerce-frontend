<template>
  <div class="modal-mask">
    <div class="modal-wrapper">
      <div class="modal-container">
        <div class="modal-header">
          <label class="" style="margin: auto; font-size: 20px"
            >Đổi mật khẩu</label
          >
        </div>
        <Form @submit="changePassword" :validation-schema="FormSchema">
          <div class="modal-body">
            <div class="row">
              <div class="form-group">
                <label for="password">Mật khẩu hiện tại: </label>
                <div>
                  <Field
                    :type="type"
                    name="password"
                    class="input form-control"
                    v-model="accountNew.password"
                    style="max-width: 300px; display: inline"
                  ></Field>
                  <div
                    class="control"
                    style="display: inline; padding-left: 10px"
                  >
                    <span class="" @click="toggleShow">
                      <i
                        class="fas"
                        :class="{
                          'fa-eye-slash': !showPassword,
                          'fa-eye': showPassword,
                        }"
                      ></i>
                    </span>
                  </div>
                </div>

                <ErrorMessage name="password" style="color: red"></ErrorMessage>
                <p style="color:red">{{ passwordMessage }}</p>

              </div>
            </div>
            <div class="row">
                                <div class="form-group">
                                    <label for="newpassword">Mật khẩu mới: </label>
                                    <div>
                                        <Field :type="typenew" name="newpassword" class="input form-control" v-model="accountNew.newpassword" style="max-width:300px; display: inline;"></Field>
                                        <div class="control" style="display: inline; padding-left:10px">
                                                                    <span class=""  @click="toggleShownew">
                                                                        <i class="fas" :class="{ 'fa-eye-slash': !shownewPassword, 'fa-eye': shownewPassword }"></i>
                                                                    </span></div></div>
                                        
                                    <ErrorMessage name="newpassword" style="color:red"></ErrorMessage>
                            </div></div>
                            <div class="row">
                                <div class="form-group">
                                    <label for="newpassword">Xác nhận mật khẩu mới: </label>
                                    <div>
                                        <Field :type="typeconfirm" name="confirmpassword" class="input form-control" v-model="accountNew.confirmpassword" style="max-width:300px; display: inline;"></Field>
                                        <div class="control" style="display: inline; padding-left:10px">
                                                                    <span class=""  @click="toggleShowconfirm">
                                                                        <i class="fas" :class="{ 'fa-eye-slash': !showconfirmPassword, 'fa-eye': showconfirmPassword }"></i>
                                                                    </span></div></div>
                                        
                                    <ErrorMessage name="confirmpassword" style="color:red"></ErrorMessage>
                                    <p style="color:red">{{ confirmMessage }}</p>
                            </div></div>
          </div>
          <div class="text-center">
            <button class="btn form-button" style="margin-right: 10px">
              Đổi mật khẩu
            </button>
            <button
              @click="$emit('close')"
              class="btn form-button"
              type="button"
              style="width: 150px"
            >
              Hủy
            </button>
          </div>
        </Form>
      </div>
    </div>
  </div>
</template>
<script>
import "@/assets/css/base.css";
import * as yup from "yup";
import { Form, Field, ErrorMessage } from "vee-validate";
import StaffService from "@/services/staff.service";
import UserService from "@/services/user.service";

export default {
  components: {
    Form,
    Field,
    ErrorMessage,
  },

  props: {
    account: { type: Object, required: true },
  },

  data() {
    const FormSchema = yup.object({
      password: yup
        .string()
        .required("Mật khẩu không được để trống"),
        newpassword: yup
        .string()
        .required("Mật khẩu không được để trống")
        .min(6, "Mật khẩu phải ít nhất 6 ký tự"),
        confirmpassword: yup
        .string()
        .required("Mật khẩu không được để trống")
        .min(6, "Mật khẩu phải ít nhất 6 ký tự"),
    });

    return {
      type: "password",
      accountNew: {},
      FormSchema,
      showPassword: false,
      typenew: "password",
      shownewPassword:false,
      typeconfirm: "password",
      showconfirmPassword:false,
      confirmMessage: '',
      passwordMessage: ''
    };
  },

  mounted() {},

  methods: {
    toggleShow() {
      this.showPassword = !this.showPassword;
      console.log(this.type);
      if (this.showPassword === true) this.type = "text";
      else this.type = "password";
    },

    toggleShownew() {
      this.shownewPassword = !this.shownewPassword;
      console.log(this.type);
      if (this.shownewPassword === true) this.typenew = "text";
      else this.typenew = "password";
    },

    toggleShowconfirm() {
      this.showconfirmPassword = !this.showconfirmPassword;
      console.log(this.type);
      if (this.showconfirmPassword === true) this.typeconfirm = "text";
      else this.typeconfirm = "password";
    },

    async changePassword(data){
        this.confirmMessage=''
        this.passwordMessage=''
        let check = await UserService.changePassword(this.account._id, data);
        
        if (check==='incorrected'){
            this.passwordMessage='Mật khẩu hiện tại không đúng'
        }
        else{
            if (check==='wrong') this.confirmMessage='Mật khẩu xác nhận không trùng khớp mật khẩu hiện tại'
            else{
                this.$toast.open({
                                message: "Đổi mật khẩu thành công",
                                type: "success",
                                duration: 3000 ,
                                dismissible: true
                            });
                            this.$emit('close');
            }
        }
    }
  },
};
</script>
<style scoped>
label {
  padding-top: 5px;
  padding-bottom: 5px;
  font-family: RalewayBold;
}

.form-button {
  background-color: var(--secondary-color);
  color: white;
  border-color: var(--secondary-color);
}
.form-control {
  border-width: 1.55px;
  border-color: var(--secondary-color);
  box-shadow: 0.5px 0.5px 7px 0.5px rgb(226, 227, 232);
}
.error-feedback {
  color: red;
}
.preview {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100px;
  width: 100px;
}
.modal-mask {
  position: fixed;
  z-index: 11;
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
  width: 410px;
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
  overflow-y: hidden;
  overflow-x: hidden;
  height: 300px;
  background-color: white;
  padding: 20px;
}
</style>
