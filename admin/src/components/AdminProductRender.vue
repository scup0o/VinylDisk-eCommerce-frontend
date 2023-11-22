<script>
    import '@/assets/css/base.css'
    import ProductForm from "@/components/ProductForm.vue"

    import ProductService from "@/services/product.service"
    export default{
        components:{
            ProductForm
        },

        props: {
            products:{
                type : Array, default: []
            }
        },

        data(){
            return{
                editProduct: null,
                edit: false,
            }
        },

        mounted(){
            console.log(this.products)
        },

        methods:{
            async deleteProduct(data){
                try{
                    if (confirm(`Bạn có chắc muốn xóa sản phẩm ${data.name}?`)){
                    console.log(await ProductService.get(data._id));
                    const check = await ProductService.delete(data._id);
                    if (check===true){
                        this.$toast.open({
                            message: "Xóa sản phẩm thành công",
                            type: "success",
                            duration: 3000 ,
                            dismissible: true
                        })
                        this.$emit('refresh');
                    }}
                }
                catch(error) {console.log(error);}
            }
            
        }
    }

</script>

<template>
    <div class="container-fluid" style="overflow-x: hidden;">
        <div style="padding-left:20px;padding-bottom:10px;" v-for="(product, index) in products" :key="product">
                    <div class="row product-container">
                        <div class="col-sm text-center" style="">
                            <img v-if="product.image" style="display: inline-flex;" class="preview img-thumbnail image" alt="" :src="`../../src/assets/img/${product.image[0]}`"/>
                        </div>
                        <div class="col-xl-3" style="">
                            <div class="" style="">
                                <div class="row" style="margin-bottom:-10px; margin-top:10px">
                                    <p v-snip="2" id="name">{{ product.name }}</p></div>
                                <div class="row">
                                    <p v-snip="3">{{ product.description }}</p></div>
                            </div>
                        </div>
                        <div class="col-2 text-center  justify-content-center" style="margin: auto">
                            <div v-if="product.discount > 0">
                                <div class="row">
                                    <p>{{ new Intl.NumberFormat('vi-VN', {
                                                        style: 'currency',
                                                        currency: 'VND'
                                                    }).format((product.price)-(product.price)*(product.discount/100)) }}</p>
                                </div>
                                <div class="row" style="text-decoration-line: line-through;">
                                    <p>{{ new Intl.NumberFormat('vi-VN', {
                                                        style: 'currency',
                                                        currency: 'VND'
                                                    }).format(product.price) }}</p>
                                </div>
                                
                                <div class="row"><p>(Giảm {{ product.discount }}%)</p></div>
                            </div>
                            <div v-else class="row">
                                    <p>{{ new Intl.NumberFormat('vi-VN', {
                                                        style: 'currency',
                                                        currency: 'VND'
                                                    }).format(product.price) }}</p>
                            </div>
                        </div>
                        <div class="col-1 text-center  justify-content-center" style="margin: auto">
                            <p>{{ product.quantity }}</p>
                        </div>
                        <div class="col text-center  justify-content-center" style="margin: auto">
                            <p>{{product.genreName }}</p>
                        </div>
                        <div class="col text-center  justify-content-center" style="margin: auto">
                            <p>{{product.artistName}}</p>
                        </div>
                        <div class="col" style="margin: auto">
                            <button class="btn btn-outline-secondary" 
                                    @click="editProduct = product, edit = true"
                                    style="margin-right:10px">
                                    <i class="fa-solid fa-pen-to-square"></i>
                            </button>
                            <button class="btn btn-outline-secondary delete-icon"
                                    @click="deleteProduct(product)">
                                    <i class="fa-solid fa-trash-can"></i>
                            </button>
                            
                        </div>
                    </div>
        </div>
    </div>
    <ProductForm v-if="edit === true"
                :product="editProduct"
                :e="true"
                @close="edit = false"
                @refresh="this.$emit('refresh')">
        
    </ProductForm>
</template>
<style scoped>
#name{
    font-family: 'RalewayBold';
    font-size:17px;
}

.product-container:hover{
    transform: scale(1.05);
    transition: transform 100ms ease-in-out;
}

.product-container{
    background-color: white;
    border-radius: 10px 10px 10px 10px;
    padding-top:10px;
}

.center{
    margin: auto;
}


.image{
    width: 120px;
    height: 120px;
    border-width: 0;
}
</style>