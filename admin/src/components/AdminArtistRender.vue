<template>
    <div class="row">
        <div class="col-3" v-for="(artist, index) in artists" :key="artist" style="padding-bottom:25px" >
            <div class="card" data-aos="fade-up" :data-aos-duration="(index+1)*200">
                <div class="row">
                    <div class="col text-center"><img style="display: inline-flex; border:none; width: 200px; height: 200px;" class="preview img-thumbnail image" alt="" :src="`../../src/assets/img/${artist.image}`"/>
                </div>
                </div>
                <div class="row">
                    <div class="col text-center"><label v-snip="1">{{ artist.name }}</label></div>
                </div>
                <div class="row" id="description-box">
                    <div class="col text-center"><p v-snip="2">{{ artist.description }}</p></div>
                </div>
                <div class="row" id="util-button">
                    <div class="col text-center" style="">
                        <button class="btn btn-outline-secondary" 
                                @click="editArtist = artist, edit = true"
                                style="margin-right:10px;">
                            <i class="fa-solid fa-pen-to-square"></i>
                        </button>
                        <button class="btn btn-outline-secondary delete-icon"
                                @click="deleteArtist(artist)">
                            <i class="fa-solid fa-trash-can"></i>
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <ArtistForm v-if="edit === true"
                :artist="editArtist"
                :e="true"
                @close="edit = false"
                @refresh="this.$emit('refresh')">
    </ArtistForm>
</template>
<script>
    import ArtistForm from "@/components/ArtistForm.vue";
    import ArtistService from "@/services/artist.service"

    export default{
        components:{
            ArtistForm
        },
        emits: ["refresh"],

        props:{
            artists:{
                type:Array, default: []
            }
        },

        data(){
            return{
                editArtist: null,
                edit: false,
            }
        },

        methods:{
            async deleteArtist(data){
                try{
                    if (confirm(`Bạn có chắc muốn xóa nghệ sĩ ${data.name}?`)){
                    const check = await ArtistService.delete(data._id)
                    if (check===false){
                        this.$toast.open({
                                message: "Xóa nghệ sĩ thất bại",
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
    height:90px;
}

#description-box{
    height: 50px;
}

#util-button{
    visibility: hidden;
}

</style>