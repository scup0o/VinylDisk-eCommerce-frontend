<template>
    <div class="modal-mask">
        <div class="modal-wrapper">
            <div class="modal-container">

                <div class="modal-header">
                    <label class="" style="margin: auto; font-size: 20px;" v-if="edit===false">Thêm sản phẩm</label>
                    <label class="" style="margin: auto; font-size: 20px;" v-else>Chỉnh sửa sản phẩm</label>

                </div>

                
                    <Form @submit="createProduct" :validation-schema="FormSchema">
                        <div class="modal-body">
                        <div class="row">
                            <div class="form-group">
                                <label for="name">Tên hàng: </label>
                                <Field name="name" type="text" class="form-control" v-model="productNew.name">
                                </Field>
                                <ErrorMessage name="name" style="color:red"></ErrorMessage>
                                <p style="color: red">{{ nameMessage }}</p>
                            </div>
                        </div>
                        <div class="row">
                            <div class="col">
                                <div class="form-group">
                                    <label for="price">Giá: </label>
                                    <Field name="price" type="number" class="form-control" v-model="productNew.price">
                                    </Field>
                                    <ErrorMessage name="price" style="color:red"></ErrorMessage>
                                </div>
                            </div>
                            <div class="col">
                                <div class="form-group">
                                    <label for="quantity">Số lượng: </label>
                                    <Field name="quantity" type="number" class="form-control" v-model="productNew.quantity">
                                    </Field>
                                    <ErrorMessage name="quantity" class="error-feedback" as="p"></ErrorMessage>
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group">
                                <label for="description">Giới thiệu sản phẩm: </label>
                                <Field as="textarea" name="description" type="text" class="form-control"
                                    v-model="productNew.description">
                                </Field>
                                <ErrorMessage name="description" class="error-feedback" as="p"></ErrorMessage>
                            </div>
                        </div>
                        <div class="row">
                            <div class="col">
                                <div class="form-group">
                                    <label for="discount">Giảm giá (%): </label>
                                    <Field name="discount" type="number" class="form-control" v-model="productNew.discount">
                                    </Field>
                                    <ErrorMessage name="discount" class="error-feedback"></ErrorMessage>
                                </div>
                            </div>
                            <div class="col">
                                <div class="form-group">
                                    <label for="discount">Sản phẩm mới: </label>
                                    
                                    <div style="margin:auto">
                                        <Field name="newRelease" type="radio" :value='true' v-model="productNew.newRelease" style="margin-left: 30px">
                                        </Field> Có
                                        <Field name="newRelease" type="radio" :value='false' v-model="productNew.newRelease" style="margin-left: 30px">
                                        </Field> Không
                                    </div>
                                    <ErrorMessage name="newRelease" class="error-feedback"></ErrorMessage>
                                </div>
                            </div>
                        </div>
                        <div class="row" style="height: 70px;">
                            <div class="form-group col">
                                <div style="position: absolute;">
                                    <label for="genreId">Thể loại: </label>
                                    <select name="genreId" v-model="productNew.genreId" class="form-control" onfocus='this.size=3;'
                                        onblur='this.size=1;' onchange='this.size=1; this.blur();' style="width: 243px">
                                        <option v-for="(genre) in genresList" v-bind:key="genre._id" v-bind:value="(genre._id).toString()">
                                            {{ genre.name }}</option>
                                    </select>
                                </div>          
                                <ErrorMessage name="genreId" class="error-feedback"></ErrorMessage>
                            </div>

                            <div class="form-group col">
                                <div style="position: absolute;">
                                <label for="artistId">Nghệ sĩ: </label>
                                    <select name="artistId" v-model="productNew.artistId" class="form-control" onfocus='this.size=3;'
                                        onblur='this.size=1;' onchange='this.size=1; this.blur();' style="width: 243px">
                                        <option v-for="(artist) in artistsList" v-bind:key="(artist._id).toString()"
                                            v-bind:value="artist._id">{{ artist.name }}</option>
                                    </select>
                                </div>
                                <ErrorMessage name="artistId" class="error-feedback"></ErrorMessage>
                            </div>
                        </div>
                        <div style="padding-top:20px">
                            <label name="image">Hình ảnh</label>
                            <label for="fileuploaded" class="btn btn-info" style="margin-left:10px; background-color: var(--secondary-color); border-color: var(--secondary-color); font-family: 'Raleway'; color:white">Chọn hình ảnh</label>
                            <form encType="multipart/form-data" @submit="this.$emit('submit')" action="/api/img" method="post">
                                
                                <input type=file name='fileuploaded' style="visibility: hidden;" @change="onFileChange" encType="multipart/form-data" id="fileuploaded" multiple="multiple">
                                
                                
                                <div class="row" style="overflow-y: scroll;height: 170px;">
                                    <div v-for="(img, index) in productNew.image" :key="img" class="col-3">
                                        <img style="display: inline-flex;" class="preview img-thumbnail" :src="`../../src/assets/img/${img}`"/>
                                        <i class="fa-solid fa-circle-xmark"
                                                style="position: relative"
                                                @click="removeImage(index)"></i>
                                    </div>
                                    <div v-for="(image, index) in imagesPreview" :key="image" class="col-3">
                                        <div style="display: inline;
                                                    width: fit-content;">
                                            <img style="display: inline-flex;" class="preview img-thumbnail" :src="image"> 
                                            <i class="fa-solid fa-circle-xmark"
                                                style="position: relative"
                                                @click="removeFileImage(index)"></i>

                                        </div>
                                    </div>
                                </div>
                            </form>
                        </div>

                        <div class="form-group" name="tracklist">
                                <label name="tracklist">Danh sách nhạc</label>
                                <div class="row">
                                    <div class="col"></div>
                                    <div class="col-1">
                                    <i class="fa-solid fa-square-plus icon"
                                        @click="createTrack = true"
                                        title="Thêm nhạc"
                                        style="font-size: 25px; margin-top:1px; color: var(--secondary-color)"></i></div> 
                                    <div class = "input-group" style="width: 300px;">
                                        <input
                                            type = "text"
                                            class ="form-control"
                                            placeholder="Nhập thông tin cần tìm"
                                            v-model = "searchText"
                                            style="font-size: 13px; height: 30px; border-color:gray; border-width: thin; box-shadow: none;"
                                        />
                                            <button class = "btn btn-outline-secondary"
                                                    type = "button"
                                                    style="border-radius: 0px 50px 50px 0px;
                                                            height: 30px;
                                                            width: 35px;
                                                            background-color: var(--secondary-color);"
                                                            >
                                                <i class = "fa-solid fa-magnifying-glass" style="font-size: 13px; margin-left: -2px; color:white"></i>
                                            </button>
                                    </div>
                                    </div>
                                    
                                <div class="modal-mask" v-if="createTrack===true">
                                    <div class="modal-wrapper">
                                        <div class="modal-container-small">

                                            <div class="modal-header">

                                            </div>
                                            <Form @submit="addTrack">
                                                <div class="modal-body-small">
                                                    <label for="track">Nhập tên bài hát: </label>
                                                    <Field name="track" type="text" class="form-control">
                                                    </Field>
                                                    <p>{{ trackmessage }}</p>
                                                </div>
                                                <div style="padding-top:30px; margin:auto" class="text-center">
                                                    <button @click="this.$emit('submit')" 
                                                            class="btn btn-info" 
                                                            style="margin-right:10px; 
                                                                    background-color: var(--secondary-color);
                                                                    border-color: var(--secondary-color);
                                                                    color:white;">Thêm</button>
                                                    <label @click="createTrack=false, trackmessage=''" 
                                                            class="btn btn-info" 
                                                            style="margin-right:10px; 
                                                                    background-color: var(--secondary-color);
                                                                    border-color: var(--secondary-color);
                                                                    color:white;
                                                                    font-family: 'Raleway';
                                                                    width:65px">Hủy</label>
                                                </div>
                                            </Form>
                                        </div>
                                    </div>
                                </div>
                                    <div class="row" style="padding-top:20px">
                                        <div class="col-6" v-if="searchText === '' " v-for="(item,index) in productNew.tracklist" :key="item">
                                            <p>{{item}}<i class="fa-solid fa-trash-can delete-icon icon" 
                                                            style="" 
                                                            @click="removeTrack(index)"></i></p>
                                            
                                        </div>
                                        <div v-if="searchText != ''" v-for="(item, index) in productNew.tracklist" :key="item">
                                            <div v-if="item.toLowerCase().includes(searchText)">
                                                <p>{{ item }}<i class="fa-solid fa-trash-can delete-icon" style="margin-left:20px;" @click="removeTrack(index)"></i></p>
                                                
                                            </div>
                                        </div>
                                    </div>
                                <ErrorMessage name="artistId" class="error-feedback"></ErrorMessage>
                        </div>
                        
                    </div>
                    <div class="text-center">
                        <button v-if="edit===false" class="btn btn-info form-button" style="margin-right: 10px">
                            Thêm sản phẩm
                        </button>
                        <button v-else class="btn btn-info form-button" style="margin-right: 10px">
                            Lưu sản phẩm
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
import ProductService from "@/services/product.service";
import GenreService from "@/services/genre.service";
import ArtistService from "@/services/artist.service";
import ImgService from "@/services/img.service"
export default {
    components: {
        Form,
        Field,
        ErrorMessage,
    },

    props: {
            product: {type: Object, required: true},
            e: {type: Boolean, required: true}
        },

    data() {

        const FormSchema = yup.object({
            name: yup
                .string()
                .required("Tên không được để trống")
                .min(2, "Tên phải ít nhất 2 ký tự.")
                .max(50, "Tên có nhiều nhất 50 ký tự."),
            quantity: yup
                .number()
                .typeError("Giá trị phải là số")
                .required('Số lượng không được để trống')
                .min(0, 'Số lượng phải >= 0'),
            price: yup
                .number()
                .typeError("Giá trị phải là số")
                .required("Giá không được để trống")
                .min(1, 'Giá phải > 0'),
        });

        return {
            edit: this.e,
            trackmessage:'',
            tempTrack: null,
            createTrack:false,
            searchText:'',
            count:(this.product.tracklist).length-1,
            trackList:[],
            searchTrack:[],
            discount: 0,
            artistID: null,
            artistsList: [],
            genreID: null,
            productNew: this.product,
            FormSchema,
            genresList: [],
            img: null,
            images: [],
            nameMessage:'',
            haveData: true,
            imagesPreview: [],
        }
    },

    mounted() {
        this.getAllGenres();
        this.getAllArtists();
        console.log(this.genresList);
        console.log(this.productNew.artistId)
    },

    create() {
    },

    methods: {
        removeFileImage(index){
            this.imagesPreview.splice(index,1);
            this.images.splice(index,1);
        },

        removeImage(index){
            this.productNew.image.splice(index, 1);
        },

        removeTrack(index){
            //console.log(index);
            if (index == 0){
                this.productNew.tracklist = this.productNew.tracklist.slice(1,(this.count)+1);
                this.count--;
            }
            else{
            const newTrackList = this.productNew.tracklist.slice(0,index);
            this.productNew.tracklist= newTrackList.concat(this.productNew.tracklist.slice(index+1, (this.count+1)));
            this.count--;
        }
            console.log(this.trackList);
        },

        addTrack(data){
            console.log(data)
            if (data.track === null || data.track === undefined || data.track === ''){
                this.trackmessage = 'Tên bài hát không được để trống'
            }else{
            this.count++;
            this.productNew.tracklist[this.count] = data.track;
            this.createTrack=false;
            console.log(this.productNew.tracklist)
            this.trackmessage=''
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
                        this.imagesPreview.push(e.target.result)
                    }
                    this.images.push(selectedFiles[i])
                    reader.readAsDataURL(selectedFiles[i]);
                    console.log(this.images);
	            }

	   // console.log(this.images);
    },
    
    /*removeImage (i) {
      // alert(i);

    	var arrayImages = this.images;
    	var index = arrayImages.indexOf(arrayImages[i]);
		  arrayImages.splice(index, i);
   
    },*/
        async onFileSelected(event){
            try{
                await ImgService.upload(event);
            }catch(error){
                console.log(error);
            }
        },

        async getAllGenres() {
            try {
                this.genresList = await GenreService.getAll();

            } catch (error) {
                console.log(error);
            }
        },

        async getAllArtists() {
            try {
                this.artistsList = await ArtistService.getAll();

            } catch (error) {
                console.log(error);
            }
        },

        async createProduct(data) {
            try {
                if (this.productNew.discount === undefined || this.productNew.discount === null || this.productNew.discount=== "") this.productNew.discount = 0;
                
                if (data.newRelease === undefined || data.newRelease === null ) data.newRelease = true;
                if (data.description === undefined || data.description === null || data.description === "") data.description = 'Không có mô tả'
                
                data.image = []

                if (this.productNew.image != null && this.productNew.image){
                    this.productNew.image.forEach((img) =>{
                        data.image.push(img);
                    })
                }

                this.images.forEach((img, index) => {
                    data.image.push(img.name);
                })


                //console.log(this.images[0]);
                //console.log(data.description);
                
                data.tracklist = [];
                //console.log(data.tracklist);
                (this.productNew.tracklist).forEach((track, index) => {
                        data.tracklist[index] = track;
                    })
                //console.log((data.trackList))
                
                data.genreId = this.productNew.genreId
                data.artistId = this.productNew.artistId;
                data.discount = this.productNew.discount;
                //console.log(data.discount)
                if (this.edit === false){
                    const check = await ProductService.create(data);
                    if (check === false){
                        this.nameMessage = 'Tên sản phẩm đã tồn tại'
                    }else{
                        const headers = { 'Content-Type': 'multipart/form-data' };
                        //let file = document.getElementById("fileuploaded").files[0];
                        //file = this.img[0];
                        //console.log(file);  
                        //console.log(this.images[0])
                        if (this.images[0] !== undefined) await ImgService.upload(this.img, { headers });
                        
                        //console.log(data);
                        //console.log(this.genreID)
                        //console.log(check);
                        this.$toast.open({
                            message: "Thêm sản phẩm thành công",
                            type: "success",
                            duration: 3000 ,
                            dismissible: true
                        })
                        this.$emit('close');
                        this.$emit('refresh');
                    }
                }
                else{
                    const check = await ProductService.update(this.productNew._id, data);
                    if (check === false){
                        console.log(check)
                        this.nameMessage = 'Tên sản phẩm đã tồn tại'
                    }
                    else{
                        console.log(data)
                        const headers = { 'Content-Type': 'multipart/form-data' };
                            //let file = document.getElementById("fileuploaded").files[0];
                            //file = this.img[0];
                            //console.log(file);  
                            //console.log(this.images[0])
                            if (this.images[0] !== undefined) await ImgService.upload(this.img, { headers });
                            
                            //console.log(data);
                            //console.log(this.genreID)
                            //console.log(check);
                            this.$toast.open({
                                message: "Chỉnh sửa sản phẩm thành công",
                                type: "success",
                                duration: 3000 ,
                                dismissible: true
                            })
                        this.$emit('close');
                        this.$emit('refresh');
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

.delete-icon:hover{
    color:red;
}

.delete-icon{
    display: inline; 
    margin-left:20px; 
    color: var(--secondary-color); 
    font-size: 15px;
}
.icon:hover{
    transform: scale(1.2);
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
		  display: inline-flex;
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

.modal-container-small {
    width: 400px;
    margin: 0px auto;
    padding: 20px 30px;
    background-color: #fff;
    border-radius: 10px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
    transition: all 0.3s ease;
    
}

.modal-header h3 {
    margin-top: 0;
}

.modal-body {
    margin: 20px 0;
    overflow-y: scroll;
    overflow-x: hidden;
    height:550px;
    background-color: white;
    padding:20px
}

.modal-body-small {
    margin: 20px 0;
    height:50px;
}

.modal-default-button {
    float: right;
}
</style>