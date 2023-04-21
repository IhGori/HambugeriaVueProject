<template>
  <NavBar :logo="logo_src" :alt="alt_name"/>
  <RouterView />
  <FooterBar />
</template>

<script>
import { RouterLink, RouterView } from 'vue-router'
import NavBar from './components/NavBar.vue';
import FooterBar from './components/FooterBar.vue';
export default{
  components:{
    NavBar,
    FooterBar
  },
  data(){
    return{
      logo_src: '/img/logo.png',
      alt_name: 'Make Your Burguer'
    }
  }
}
</script>


<style>
  *{
    font-family: Helvetica;
    padding: 0;
    margin: 0;
    box-sizing: border-box;
  }

  .main-container{
    margin: 50px;
    min-height: 250px;
  }

  h1{
    text-align: center;
    font-size: 42px;
    margin-bottom: 30px;
    color: #222;
  }
</style>
