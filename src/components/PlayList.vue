<template>


<div class="container">
    <ul class="playlist"> 
      <li @click="selsong(index,song.imgurl,song.audiourl,song.title)" 
      v-for="(song,index) in list" :key="index"
      :class="{'hover':hoverIdx===index}"
      >
        {{ song.title }} 
      </li>
    </ul>
</div>
</template>


<script>

import {eventBus} from "../eventBus";

export default {
  data(){
    return{
      hoverIdx:0,
      list:[
        {
          imgurl:"Mipha's Theme.jpg",
          audiourl:"miphas-theme-the-legend-of-zelda-breath-of-the-wild-ost.mp3",
          title:"Mipha's Theme (The Legend of Zelda: Breath of the Wild OST)"
        },
        {
          imgurl:"01-through-the-arbor.jpg",
          audiourl:"01-through-the-arbor.mp3",
          title:"01 Through the Arbor 走過綠意- 凱文柯恩"
        },
        {
          imgurl:"01-through-the-arbor.jpg",
          audiourl:"sundial-dreams.mp3",
          title:"02 Sundial Dreams 日晷之夢- 凱文柯恩"
        },
        {
          imgurl:"chibli.jpg",
          audiourl:"chibli-witches.mp3",
          title:"能看見海的街道 (源於《魔女宅急便》)"
        },
        {
          imgurl:"chibli.jpg",
          audiourl:"chibli-dodoro.mp3",
          title:"鄰家的龍貓 (鋼琴曲) (源於《龍貓》)"
        },
        {
          imgurl:"chibli.jpg",
          audiourl:"chibli-aircity.mp3",
          title:"伴隨著你 (鋼琴曲) (源於《天空之城》)"
        }
      ]
    }
  },
  created(){
    eventBus.off('nextSong');
    eventBus.on('nextSong',(idx)=>{
      if (this.list[idx]==undefined) {
        eventBus.emit("endidx",idx);
        return;
      }
      this.hoverIdx = idx;
      let pic = this.list[idx].imgurl;
      let music = this.list[idx].audiourl;
      let title = this.list[idx].title ;
      this.selsong(idx,pic,music,title);
    })
  },
  methods:{
    selsong(index,pic,music,title){
      this.hoverIdx = index;
      let imgurl = "img/" + pic;
      let audiourl = "audio/" + music;
      eventBus.emit("changeSong",{index,imgurl,audiourl,title});
    }
  }


}

</script>


<style scoped>
*{
  list-style-type: none;
}
.container{
  box-shadow: 7px 7px 10px 1px #2F2E3C;
  border-radius: 10px;
}
.playlist{
  background-color: #3B3F4A;
  box-sizing: border-box;
  padding: 10px;
  border-radius: 10px;
}
.playlist li{
    background-color: #3B3F4A;
    padding: 5px;
    transition: all 0.3s ease;
    margin: 5px 0;
    padding: 10px;
}
.playlist li:hover{
    background-color: #dddddd;
    color: black;
    cursor: pointer;
}
.playlist .hover{
    background-color: #dddddd;
    color: black;
}
</style>