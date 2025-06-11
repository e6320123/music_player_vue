<template>

<div class="app-container" @click="getwidth">
  <div class="mainpanel">
    <MainPanel></MainPanel>
  </div>
  <div class="playlist">
    <PlayList></PlayList>
  </div>
</div>

</template>

<script>
import MainPanel from "./components/MainPanel"
import PlayList from "./components/PlayList"
export default {
  data(){
    return {
    }
  },
  components:{
    MainPanel,
    PlayList 
  },
  methods:{
    getwidth(){
      // alert(window.innerWidth);
    }
  }
}
</script>

<style scoped>

.app-container {
  /* border: 4px solid blue;  */
  width: 1056px; 
  overflow: hidden;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%,-50%);
  /* margin: 0 30px; */
}

.mainpanel {
  float: left;
  width: 50%;
}

.playlist {
  float: left;
  width: 50%;
  color: aliceblue;
}

@media screen and (max-width:1056px) {
  .app-container {
    width: 100%;
    overflow: scroll; 
    position: relative; 
    transform: none;
    top: 0;
    left: 0;
}

  .mainpanel {
    float: none;
    width: 100%;
  }

  .playlist {
    float: none;
    width: 100%;
    color: aliceblue;
  } 

}


</style>
