<template>

        <div class="mt-5" height="600px">
        <div class="row justify-content-around">
            <div class="col mt-5">
                <div class="title animated fadeInRight" >
                            <h2>Los freelancers más rápidos del mundo.</h2>
                            <p>Los freelancers mas rapidos, mas eficientes y mas listos</p>
                    </div>
                    <button 
                    class="btn btn-primary btn-search animated fadeInLeft"
                    @click="$router.push('/freelancers')"
                    >Buscar Freelancers</button>
            </div>
            <div class="col animated fadeInRight">
            <img class="img-carousel" src="@/assets/img/3.svg" alt="" width="685px">               
            </div>
        </div>
         </div>
</template>

<script>
export default {
  name: "Carousel",
  date() {
    return {};
  }
};
</script>

<style>
.el-carousel__item  {
  color: #80869b;
}

.title{
    margin-top: 100px;
    animation-duration: 1s;
    animation-delay: 1s;
}

@media only screen and (max-width: 600px) {
    .title {
        margin-top: -50px;
    }
    .img-carousel {
        margin-top: 50px;
        width: 300px;
    }
}
.btn-search{
    border: none;
    outline: none;
    animation-duration: 1.4s;
    animation-delay: 1.4s;
}
.btn-search:hover{
    background: #4960bb;

}
</style>
