<template>
    <div class="container-fluid">  
        <div class="row" style="padding-bottom: 20px;">
            <div class="col-5">
                <span>Danh sách sản phẩm</span>
            </div>
        </div>
        <div class="row">
            <div class="col">
                <button class="btn btn-outline-secondary" 
                        @click="createForm = true"
                        data-aos="fade-up"
                        style="margin-right:10px">
                    Thêm sản phẩm
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
            <div class="col text-end" >     
                    <button class="btn btn-outline-secondary" id="filter-parent" >
                        Lọc
                        <i class="fa-solid fa-filter"></i>
                        <div id="filter-child" style="font-size: 15px;">
                            <div style="overflow-y: scroll; height:190px; overflow-x: hidden;">
                                <div class="row" > 
                                    <div class="col-6 border-rigth">
                                        <div class="row">
                                            <label style="font-family: 'RalewayBold'; padding-bottom:10px">Thể loại</label>
                                            <div class="col-6 text-start" v-for="(genre) in genreList" :key="genre._id">
                                                <input id="gname" type="radio" v-model="filterGenre" :value="genre.name">
                                                {{ genre.name }}
                                            </div>
                                        </div>
                                        
                                    </div> 
                                    <div class="col">
                                     <div class="vr" style="height: 100px;"></div></div>
                                    <div class="col-5" style="">
                                        <div class="row">
                                            <label style="font-family: 'RalewayBold'; padding-bottom:10px">Nghệ sĩ</label>
                                            <div class="col-6 text-start" v-for="(artist) in artistList" :key="artist._id">
                                                <input  type="radio"  v-model="filterArtist" :value="artist.name">
                                                {{ artist.name }}
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="col" style="padding:10px">
                                <button class="btn btn-outline-secondary" style="" @click="uncheckAll">Bỏ lọc</button>
                            </div>
                        </div>
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
            <ProductForm v-if="createForm === true" 
                        @close="createForm = false; "
                        @refresh="retrieveProduct()"
                        :e="false"
                        :product="{
                            name:null,
                            price:null,
                            description: null,
                            genreId: null,
                            quantity: null,
                            image: null,
                            discount: 0,
                            tracklist: [],
                            newRelease: true,
                            artistId: null,
                        }"></ProductForm>
            
        </div>
        <div class="row" style="margin-left:20px; font-size: 15px; margin-top:20px; margin-bottom:-10px">
            <div class="row" data-aos="fade-up" style="font-family: 'RalewayBold';">
                        <div class="col-sm text-center justify-content-center" style="margin: auto">
                            HÌNH ẢNH
                        </div>
                        <div class="col-xl-3 text-center justify-content-center" style="margin: auto">
                            TÊN
                        </div>
                        <div class="col-2 text-center justify-content-center" style="margin: auto">
                            GIÁ
                        </div>
                        <div class="col-1 text-center justify-content-center" style="margin: auto">
                            SỐ LƯỢNG
                        </div>
                        <div class="col text-center justify-content-center" style="margin: auto">
                            THỂ LOẠI
                        </div>
                        <div class="col text-center justify-content-center" style="margin: auto">
                            NGHỆ SĨ
                        </div>
                        <div class="col text-center justify-content-center" style="margin: auto">
                            CÔNG CỤ
                        </div>
                    </div>
        </div>
        <div class="row" style="padding-top:20px" >
            <div v-for="(products, index) in sliceProduct" :key="index" v-if="filteredProductsCount > 0">
                <AdminProductRender v-if=" index===onPage"
                                    :products="products"
                                    @refresh="retrieveProduct()">
                </AdminProductRender>
                
            </div>
            <p v-else>Không có sản phẩm nào.</p>
        </div>
        <div class="row text-center d-flex" v-if="filteredProductsCount > 0">
            <div >
            <button v-if="onPage!=0" 
                    @click="onPage--, onPageTemp--" 
                    class="btn" 
                    style=" margin-right:10px; background-color: none; border: none" 
                    title="Trang trước"><i class="fa-solid fa-caret-left"></i></button>
            <div style="display:inline"><input class="form-control" style="width:50px; display: inline;" type="number" v-model="onPageTemp" @keyup.enter="onPage=(onPageTemp-1), onPageTemp=(onPage+1)">/ {{ pageNumber }}</div>
            <button v-if="onPageTemp <pageNumber" 
                    @click="onPage++, onPageTemp=(onPage+1)" 
                    class="btn" 
                    style=" margin-left:10px; border: none" 
                    title="Trang sau"><i class="fa-solid fa-caret-right"></i></button>
            </div>
        </div>
    </div>
    
</template>
<script>
    import '@/assets/css/base.css'
    import ProductForm from "@/components/ProductForm.vue"
    import AdminProductRender from '@/components/AdminProductRender.vue';

    import ProductService from "@/services/product.service";
    import GenreService from "@/services/genre.service"
    import ArtistService from "@/services/artist.service"

    export default{
        components:{
            ProductForm,
            AdminProductRender,
        },

        data(){
            return{
                createForm: false,
                searchText: '',
                products: [{
                }],
                productsList: [],
                onPage: 0,
                pageNumber: 0,
                onPageTemp: 1,
                genreList: [],
                artistList: [],
                filterGenre: '',
                filterArtist: '',
            }
        },

        mounted(){
            this.retrieveProduct();
        },

        computed:{
            updateonPage(e){
                this.onPage = parseInt(e.target.value);
                this.onPageTemp = this.onPage++
            },
            
            productStrings(){
                return this.products.map((product) =>{
                    const {name, genreName, artistName } = product;
                    return [name, genreName, artistName].join("");
                });
            },

            filteredProducts(){
                if (!this.searchText && !this.filterArtist && !this.filterGenre) return this.products;
                else{
                    this.onPage = 0;
                    this.onPageTemp = 1;
                    console.log(this.filterArtist);
                    console.log(this.filterGenre)
                    let tempProductList = this.products.filter((_product, index) => this.productStrings[index].toLowerCase().includes(this.searchText.toLowerCase()));
                    
                    const p = [];
                    if ((this.filterArtist!='') || (this.filterGenre!='')){
                        console.log('have')
                        if ((this.filterArtist!='') && (this.filterGenre!='')){
                            tempProductList.forEach((product, index) =>{
                                if ((product.genreName === this.filterGenre) && (product.artistName === this.filterArtist)){
                                    p.push(product)
                                    console.log(product);
                                }
                            })
                        }
                        else{
                            tempProductList.forEach((product, index) =>{
                                if ((product.genreName === this.filterGenre) || (product.artistName === this.filterArtist)){
                                    p.push(product)
                                    console.log(product);
                                }
                            })
                        }
                        return p;
                    }
                    return tempProductList;
                }
            },

            filteredProductsCount(){
                return this.filteredProducts.length
            },

            sliceProduct(){
                //let pL = this.filteredProducts;
                let productListNumber = Math.ceil(this.filteredProducts.length / 6);
                this.pageNumber = productListNumber
                console.log(productListNumber)
                let count = 0;
                let tempProducts = []
                let i;
                for (i=0; i<productListNumber; i++){
                    tempProducts[i] = this.filteredProducts.slice(count,count+6);
                    count=count+6;
                    if (count > this.filteredProducts){

                    }
                    console.log(tempProducts);
                }
                this.productsList = tempProducts;
                console.log(this.productsList)
                return tempProducts;

            }
        },

        methods:{
            uncheckAll(){
                this.filterArtist = '';
                this.filterGenre ='';
            },

            async deleteAll(){
                if (confirm("Bạn muốn xóa tất cả Sản phẩm?")){
                    try{
                        const deleteCount = await ProductService.deleteAll();
                        if (deleteCount.deletedCount!=0){
                            console.log(deleteCount);
                            this.$toast.open({
                                message: `Xóa ${deleteCount.deletedCount} sản phẩm thành công`,
                                type: "success",
                                duration: 3000 ,
                                dismissible: true
                            })
                            this.retrieveProduct();
                        }
                    }catch(error){
                        console.log(error)
                    }
                }
            },

            async retrieveProduct(){
                try{
                    this.products = await ProductService.getAll();
                    //console.log(this.products)
                    this.products.forEach( async (product, index) => {
                        const tempGerneName = await GenreService.get(product.genreId);
                        product.genreName = tempGerneName.name;
                        //console.log(product.genreName)
                        const tempArtistName = await ArtistService.get(product.artistId);
                        product.artistName = tempArtistName.name;
                    })
                    
                    this.genreList = await GenreService.getAll();
                    this.artistList = await ArtistService.getAll();
                    
                }catch(error){
                    console.log(error);
                }
            },

            

        }
    }
</script>
<style scoped>
span{
    font-family: 'RalewayBold';
    font-size: 25px;
}

#util-icon{
    margin-left: 10px;
}

#filter-parent:hover #filter-child{
    visibility: visible;
}

#filter-child:hover{
    visibility: visible;
}

#filter-child{
    visibility: hidden;
    width: 600px;
    height: 260px;
    position: absolute;
    color:black;
    background-color: white;
    margin-left:-113px;
    z-index:99;
    margin-top:7px;
    border-radius: 10px 10px 10px 10px;
    padding:15px;
    box-shadow: 0.5px 0.5px 7px 0.5px rgb(226, 227, 232);
}

</style>