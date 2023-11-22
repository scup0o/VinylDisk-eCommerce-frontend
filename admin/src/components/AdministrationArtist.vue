<template>
    <div class="container-fluid">
        <div class="row" style="padding-bottom: 20px;">
            <div class="col-5">
                <span>Danh sách nghệ sĩ</span>
            </div>
        </div>
        <div class="row">
            <div class="col">
                <button class="btn btn-outline-secondary" 
                        @click="createForm = true"
                        data-aos="fade-up"
                        style="margin-right:10px">
                    Thêm nghệ sĩ
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
            <ArtistForm v-if="createForm === true" 
                        @close="createForm = false"
                        @refresh="retrieveArtist()"
                        :e="false"
                        :artist="{
                            name: null,
                            description: null,
                            image:null,
                        }">

            </ArtistForm>
        </div>
        <div class="row" style="padding-top:20px" >
            <div v-for="(artist, index) in sliceArtist" :key="index" v-if="filteredArtistsCount > 0">
                <AdminArtistRender v-if=" index===onPage"
                                    :artists="artist"
                                    :is="artist"
                                    @refresh="retrieveArtist()">
                </AdminArtistRender>
                
            </div>
            <p v-else>Không có nghệ sĩ nào.</p>
        </div>
        <div class="row text-center d-flex" v-if="filteredArtistsCount > 0">
            <div >
            <button v-if="onPage!=0" 
                    @click="onPage--, onPageTemp--" 
                    class="btn" 
                    style=" margin-right:10px; border-color:none" 
                    title="Trang trước"><i class="fa-solid fa-caret-left"></i></button>
            <div style="display:inline"><input class="form-control" style="width:50px; display: inline;" type="number" v-model="onPageTemp" @keyup.enter="onPage=(onPageTemp-1), onPageTemp=(onPage+1)">/ {{ pageNumber }}</div>
            <button v-if="onPageTemp <pageNumber" 
                    @click="onPage++, onPageTemp=(onPage+1)" 
                    class="btn" 
                    style=" margin-left:10px;border-color: none" 
                    title="Trang sau"><i class="fa-solid fa-caret-right"></i></button>
            </div>
        </div>
    </div>
</template>
<script>
    import '@/assets/css/base.css'
    import ArtistForm from "@/components/ArtistForm.vue"
    import ArtistService from "@/services/artist.service"
    import AdminArtistRender from "@/components/AdminArtistRender.vue"

    export default{
        components:{
            ArtistForm,
            AdminArtistRender,
        },

        

        data(){
            return{
                artists: [],
                artistsList: [],
                createForm: false,
                searchText: '',
                onPage: 0,
                pageNumber: 0,
                onPageTemp: 1,
            }
        },

        mounted(){
            this.retrieveArtist();
        },

        computed:{
            artistStrings(){
                return this.artists.map((artist) =>{
                    const {name} = artist;
                    return [name].join("");
                });
            },

            filteredArtists(){
                if (!this.searchText) return this.artists;
                return this.artists.filter((_artist, index) => this.artistStrings[index].toLowerCase().includes(this.searchText.toLowerCase()));
                
            },

            filteredArtistsCount(){
                //console.log(this.filteredArtists.length);
                return this.filteredArtists.length
            },

            sliceArtist(){
                let artistListNumber = Math.ceil(this.filteredArtists.length / 8);
                this.pageNumber = artistListNumber
                let count = 0;
                let tempArtists = []
                let i;
                for (i=0; i<artistListNumber; i++){
                    tempArtists[i] = this.filteredArtists.slice(count,count+8);
                    count=count+8;
                }
                this.artistsList = tempArtists;
                return tempArtists;

            }
        },

        methods:{
            async deleteAll(){
                if (confirm("Bạn muốn xóa tất cả Nghệ sĩ?")){
                    try{
                        const deleteCount = await ArtistService.deleteAll()
                        if (deleteCount.deletedCount!=0){
                            this.$toast.open({
                                    message: `Xóa ${deleteCount.deletedCount} nghệ sĩ thành công`,
                                    type: "success",
                                    duration: 3000 ,
                                    dismissible: true
                                })
                            this.retrieveArtist()
                        }
                        
                    }
                    catch(error){
                        console.log(error)
                    }
                }
            },
            
            async retrieveArtist(){
                try{
                    this.artists = await ArtistService.getAll();
                    console.log(this.artists)
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