<template>
<div>
<div class="nav-admin" >
<div>
  <el-menu default-active="2" class="el-menu-vertical-demo el-menu-responsive mb-5"  :collapse="false">
  <el-submenu :style="{width:'100%'}" class="text-white" index="1">
      <el-row slot="title" type="flex" justify="space-around" :gutter="40" class="title-responsive">
  <el-col :xs="20" :sm="6" :md="4" :lg="3" :xl="1">
    <h5 class="text-white  title-responsive">SpeedWork</h5>   
  </el-col>
    <el-col :xs="10" :sm="6" :md="4" :lg="3" :xl="1">
    <h2 class="fas fa-bars btn-menu"></h2>   
  </el-col>
  </el-row>
    <el-menu-item-group class="el-menu-item-responsive">
   <el-menu-item index="1-1"  @click="$router.push('/inicio')"><span class="" >Home</span></el-menu-item>
  <el-menu-item index="1-2" @click="$router.push('/proyectos')" > <span class=""> Proyectos </span></el-menu-item>
  <el-menu-item index="1-3"  @click="$router.push('/freelancers')" ><span class="">Freelancers</span></el-menu-item>
  <el-menu-item index="1-4"  @click="$router.push('/buscar/freelancers')" ><span class="">Buscar Freelancers</span></el-menu-item>
  <el-menu-item index=""  @click="$router.push('/proyecto/publicar')" ><span class="">Publicar Proyecto</span></el-menu-item>
  <el-menu-item index="1-5" v-show="register" @click="$router.push('/registro')"><span class="nav-text">Registrate</span></el-menu-item>    
  <el-menu-item index="1-6" @click="redirecProfileOrLogin()"  v-if="UserName === 'Inicia Sesión'" >
    <div >
    {{UserName}}
    </div>
  </el-menu-item>
    <div class="mr-4" v-if="UserName !== 'Inicia Sesión'">
     <el-submenu :style="{width:'100%'}" index="2">
    <span slot="title" >{{UserName}}</span>
    <el-menu-item index="2-1" > <router-link tag="span" :to="`/freelancer/${userId}`">
Mi Perfil
</router-link></el-menu-item>
    <el-menu-item index="2-1" v-if="admin() == 2" > <router-link tag="span" :to="`/dashboardAdmin`">
Panel Administrativo
</router-link></el-menu-item>
    <el-menu-item index="2-2"><span @click="logout()">Salir</span></el-menu-item>
    </el-submenu>
  </div>

    </el-menu-item-group>
  </el-submenu>
</el-menu>
</div>
</div>
<div class="nav">
  <el-menu :default-active="activeIndex"  class="el-menu-demo nav-header " mode="horizontal" >
  <el-menu-item index="1" class="logo" @click="$router.push('/inicio')">
  <img src="@/assets/flash.png" width="28" height="28"><span class="title1" >SpeedWork</span></el-menu-item>
  <el-menu-item index="2" class="proyect " @click="$router.push('/proyectos')" > <span class="nav-text"> Proyectos </span></el-menu-item>
  <el-menu-item index="3" class="freelancers" @click="$router.push('/freelancers')"><span class="nav-text">Freelancers</span></el-menu-item>
  <el-menu-item index="4" class="search-freelancers" @click="$router.push('/buscar/freelancers')" ><span class="">Buscar Freelancers</span></el-menu-item>
  <el-menu-item index="5" class="shared-proyect" @click="$router.push('/proyecto/publicar')" ><el-button type="primary" class="btn-nav" round>Publicar Proyecto</el-button></el-menu-item>
  <el-menu-item index="6" v-show="register" class="register " @click="$router.push('/registro')"><span class="nav-text">Registrate</span></el-menu-item>    
  <el-menu-item index="7" class="login"><span class="nav-text">
    
    <div v-if="UserName !== 'Inicia Sesión' ">
      <el-dropdown :hide-on-click="true">
  <span class="el-dropdown-link">
    {{UserName}}<i class="el-icon-arrow-down el-icon--right"></i>
  </span>
  <el-dropdown-menu slot="dropdown">
   <el-dropdown-item v-if="admin() == 1">
<span @click="profile">Perfil</span>
</el-dropdown-item>
    <el-dropdown-item v-if="admin() == 2">
      <span @click="redirect('/dashboardAdmin')">
        Panel Administrativo
      </span>
    </el-dropdown-item>    
    <el-dropdown-item divided><span @click="logout()">Salir</span></el-dropdown-item>
  </el-dropdown-menu>
</el-dropdown>
    </div>    
    <div @click="redirecProfileOrLogin()" v-else>
    {{UserName}}
    </div>  
    </span></el-menu-item>
</el-menu>
</div>
</div>
</template>
<script>
import { EventBus } from "../../helpers/event-bus";
export default {
  name: "Nav1",
  data() {
    return {
      activeIndex: "1",
      isCollapse: true,
      path: null
    };
  },
  updated(){
    this.admin()
  },
  mounted(){
    // EventBus.$once('reset',()=>{
    //   this.logout()
    // })
  },
  methods: {
    redirect(path) {
      if (path === undefined) return;
      this.$router.push(path);
    },
    redirecProfileOrLogin() {
      this.path =
        this.$store.getters.loggedIn != false ? "/freelancer/nombre" : "/login";
      this.redirect(this.path);
    },
    collapse() {
      if (this.isCollapse) {
        this.isCollapse = false;
      } else {
        this.isCollapse = true;
      }
    },
    logout() {
      let self = this;
      let tokenName = "access_token";
      let tokenUserName = "user_info";
      let response = self.$store.state.services.authService.logout(
        tokenName,
        tokenUserName
      );
      //si se removio el token se desloguea
      if (response) {
        //removemos el token del state
        self.$store.state.token = null;
        this.redirect("/login");
      } else {
        console.log("ocurrio un error");
      }
    },
    /**
     * envia un evento al momento de ir al perfil
     * para hacer una nueva peticion de los datos
     */
    profile() {
      EventBus.$emit("profile", this.userId);
      this.$router.push(`/freelancer/${this.userId}`);
    },
    /**
     * retorna el rol del usuario admin/normal
     */
    admin(){
      return localStorage.getItem('user_role')
    }
  },
  computed: {
    UserName() {
      let name =
        this.$store.getters.loggedIn != false
          ? localStorage.getItem("user_info")
          : "Inicia Sesión";
      return name;
    },
    register() {
      return this.UserName != "Inicia Sesión" ? false : true;
    },
    /**
     * esta funcion es para traer el id que esta en
     * el estado de los componenetes y se encuentra en el
     * localStorage
     */
    userId() {
      let name =
        this.$store.getters.loggedIn != false
          ? localStorage.getItem("user_id")
          : null;
      return name;
    }
  }
};
</script>
<style scoped>
.nav-text.scrolled {
  color: #5a75e6 !important;
  transition: background-color 1500ms linear;
}

.logo.scrolled,
.logo.scrolled {
  color: #000 !important;
}

.login {
  position: absolute;
  left: 85%;
}
.register {
  position: absolute;
  left: 75%;
}
.shared-proyect {
  position: absolute;
  left: 57%;
}
.proyect {
  position: absolute;
  left: 25%;
}
.freelancers {
  position: absolute;
  left: 15%;
}
.search-freelancers {
  position: absolute;
  left: 33%;
}
.logo {
  position: relative;
  left: 2%;
}
.title1 {
  font-size: 20px;
  color: #5a75e6;
}
.nav-text {
  font-family: "Roboto", sans-serif;
}
.nav-text:hover {
  color: #5a75e6;
}
.nav-header {
  width: 100%;
  background: #fff;
  position: fixed;
  z-index: 1;
  transition: background-color 200ms linear;
}
.btn-nav {
  background: #5a75e6;
  color: #fff;
  border: none;
  font-family: "Roboto", sans-serif;
  outline: none;
}
.btn-nav:hover{
    background: #4960bb;
}
@media (max-width: 900px) {
  .nav {
    display: none !important;
    visibility: hidden !important;
  }
}
@media (min-width: 900px) {
  .nav-admin {
    display: none;
  }
}
.ocultar {
  display: none;
}
.btn-collapse {
  background: #fff;
  position: absolute;
  left: 91%;
}
.el-menu {
  color: #fff;
}
.el-menu-responsive {
  background: #5a75e6;
  position: fixed;
  z-index: 1;
  width: 100%;
}
.title-responsive {
  position: relative;
  right: 10%;
  padding: 6px;
  background: #5a75e6;
}
.btn-menu {
  position: absolute;
  color: #fff;
  left: 93%;
  margin: -10px;
  cursor: pointer;
  width: 20%;
  background: #5a75e6;
  padding-top: 14px;
  padding-block-end: 14px;
}
.el-menu-vertical-demo {
  z-index: 1000;
}
.nav {
  z-index: 1000;
}
</style>
