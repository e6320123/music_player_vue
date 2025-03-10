<template>
<div class="container">

  <audio id="nowplay" @loadedmetadata="duration()" @timeupdate="progbar()" @ended="chnext()" >
    <source id="mp3src" :src="audiourl" type="audio/mpeg">
  </audio>
  
  <div class="player-panel">
    <img :src="imgurl">
    <br>
    <div class="progress">
      <span class="now-time">{{now_m}}:{{now_s}}</span>
      <!-- <input id="progbar" class="prog-bar" type="range" @input="jump($event.target.value)" value="0"> -->
      <div @click="jump($event)" class="progress-container" id="progressContainer">
        <div class="progress-bar" id="progressBar"></div>
        <div @mousedown="drag($event)" class="progress-handle" id="progressHandle" draggable="false"></div>
      </div>
      <span class="total-time">{{duration_m}}:{{duration_s}}</span>
    </div>
    <h3>{{ title }}</h3>
    <br>
    <div class="ctrl-panel">
      <button class="palybtn" @click="play()">
        <span>
          <!-- <svg fill="#F4EFE9" xmlns="http://www.w/2000/svg" viewBox="65 40 320 512"> -->
          <svg fill="#F4EFE9" xmlns="http://www.w3.org/2000/svg" viewBox="-10 0 384 512">
            <path d="M73 39c-14.8-9.1-33.4-9.4-48.5-.9S0 62.6 0 80L0 432c0 17.4 9.4 33.4 24.5 41.9s33.7 8.1 48.5-.9L361 297c14.3-8.7 23-24.2 23-41s-8.7-32.2-23-41L73 39z"/></svg>
        </span>
      </button>
      <button class="pausebtn" @click="pause()">
        <span>
          <!-- <svg fill="#F4 xmlns="http://www.w3.org/2000/svg" viewBox="0 40 320 512"> -->
          <svg fill="#F4EFE9" xmlns="http://www.w3.org/2000/svg" viewBox="0 40 320 512">
            <path d="M48 64C21.5 64 0 85.5 0 112L0 400c0 26.5 21.5 48 48 48l32 0c26.5 0 48-21.5 48-48l0-288c0-26.5-21.5-48-48-48L48 64zm192 0c-26.5 0-48 21.5-48 48l0 288c0 26.5 21.5 48 48 48l32 0c26.5 0 48-21.5 48-48l0-288c0-26.5-21.5-48-48-48l-32 0z"/></svg>
        </span>
      </button>
      <button class="prebtn" @click="chpre()">
        <span>
          <svg fill="#F4EFE9" xmlns="http://www.w3.org/2000/svg" viewBox="0 40 320 512">
            <path d="M267.5 440.6c9.5 7.9 22.8 9.7 34.1 4.4s18.4-16.6 18.4-29l0-320c0-12.4-7.2-23.7-18.4-29s-24.5-3.6-34.1 4.4l-192 160L64 241 64 96c0-17.7-14.3-32-32-32S0 78.3 0 96L0 416c0 17.7 14.3 32 32 32s32-14.3 32-32l0-145 11.5 9.6 192 160z"/></svg>
        </span>
      </button>
      <button class="nextbtn" @click="chnext()">
        <span>
          <svg fill="#F4EFE9" xmlns="http://www.w3.org/2000/svg" viewBox="0 40 320 512">
            <path d="M52.5 440.6c-9.5 7.9-22.8 9.7-34.1 4.4S0 428.4 0 416L0 96C0 83.6 7.2 72.3 18.4 67s24.5-3.6 34.1 4.4l192 160L256 241l0-145c0-17.7 14.3-32 32-32s32 14.3 32 32l0 320c0 17.7-14.3 32-32 32s-32-14.3-32-32l0-145-11.5 9.6-192 160z"/></svg>
        </span>
      </button>
    <!-- <span>&emsp;音量</span> -->
       <input class="volume" type="range" @input="volume($event.target.value)" value="50">
       <svg class="volume-icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 640 512">
         <path d="M533.6 32.5C598.5 85.2 640 165.8 640 256s-41.5 170.7-106.4 223.5c-10.3 8.4-25.4 6.8-33.8-3.5s-6.8-25.4 3.5-33.8C557.5 398.2 592 331.2 592 256s-34.5-142.2-88.7-186.3c-10.3-8.4-11.8-23.5-3.5-33.8s23.5-11.8 33.8-3.5zM473.1 107c43.2 35.2 70.9 88.9 70.9 149s-27.7 113.8-70.9 149c-10.3 8.4-25.4 6.8-33.8-3.5s-6.8-25.4 3.5-33.8C475.3 341.3 496 301.1 496 256s-20.7-85.3-53.2-111.8c-10.3-8.4-11.8-23.5-3.5-33.8s23.5-11.8 33.8-3.5zm-60.5 74.5C434.1 199.1 448 225.9 448 256s-13.9 56.9-35.4 74.5c-10.3 8.4-25.4 6.8-33.8-3.5s-6.8-25.4 3.5-33.8C393.1 284.4 400 271 400 256s-6.9-28.4-17.7-37.3c-10.3-8.4-11.8-23.5-3.5-33.8s23.5-11.8 33.8-3.5zM301.1 34.8C312.6 40 320 51.4 320 64l0 384c0 12.6-7.4 24-18.9 29.2s-25 3.1-34.4-5.3L131.8 352 64 352c-35.3 0-64-28.7-64-64l0-64c0-35.3 28.7-64 64-64l67.8 0L266.7 40.1c9.4-8.4 22.9-10.4 34.4-5.3z"/></svg>
     </div>
  </div>
</div>
</template>


<script>

import {eventBus} from "../eventBus";

export default {
  data(){
    return{
      index:0,
      title:"Mipha's Theme (The Legend of Zelda: Breath of the Wild OST)",
      imgurl:"img/Mipha's Theme.jpg",
      audiourl:"audio/miphas-theme-the-legend-of-zelda-breath-of-the-wild-ost.mp3",
      now_m:"00",
      now_s:"00",
      duration_m:"00",
      duration_s:"00",
      time_flag:'',
      isDragging:false

    }
  },
  created(){ 
    eventBus.off('changeSong');
    eventBus.on('changeSong',(res)=>{
      this.index = res.index
      this.imgurl=res.imgurl;
      this.audiourl=res.audiourl;
      this.title = res.title;
      this.now_m = "00";
      this.now_s = "00";
      //進度條歸零
      document.getElementById('progressBar').style.width = "0%";
      document.getElementById('progressHandle').style.left = "0%";
      clearInterval(this.time_flag);
      document.getElementById('nowplay').load();
      this.play();
    })
    eventBus.off('endidx');
    eventBus.on('endidx',(endidx)=>{
      this.index = endidx-1;
    })
  },
  methods:{
    play(){
      let nowplay = document.getElementById('nowplay');
      nowplay.play();
      this.updateTime();
    },
    pause(){
      let nowplay = document.getElementById('nowplay');
      nowplay.pause();
      clearInterval(this.time_flag);
    },
    gettime(){
      let nowplay = document.getElementById('nowplay');
      console.log(nowplay.currentTime);
    },
    chpre(){
      if (this.index!=0) {
        this.index--;
        eventBus.emit("nextSong",this.index);
      }
    },
    chnext(){
      this.index++;
      eventBus.emit("nextSong",this.index);
    },
    volume(chgvo){
      console.log(chgvo);
      let volume = chgvo/100;
      document.getElementById('nowplay').volume = volume/2;
    }, 
    updateTime(){
      let _this = this;
      this.time_flag = setInterval(function(){
        let nowplay = document.getElementById('nowplay');
        let minute = Math.floor(nowplay.currentTime/60);
        _this.now_m = minute<10?'0'+minute:minute;
        let sec = Math.floor(nowplay.currentTime%60);
        _this.now_s = sec<10?'0'+sec:sec; 
        console.log("setInterval on...");
      },500)
    },
    progbar(){
      let nowplay = document.getElementById('nowplay');
      let progbar = document.getElementById('progressBar')
      let proghandle = document.getElementById('progressHandle')
      let current = (nowplay.currentTime/nowplay.duration)*100
      if(isNaN(current)||this.isDragging) return;
      progbar.style.width = current + "%";
      proghandle.style.left = current + "%";
    },
    jump(e){
      if(this.isDragging) return;
      let nowplay = document.getElementById('nowplay');
      let progCon = document.getElementById('progressContainer');
      let rect = progCon.getBoundingClientRect();
      let offsetX = e.clientX - rect.left;
      let percent = offsetX/rect.width;
      let current = nowplay.duration*percent;
      nowplay.currentTime = current;
      //進度條修改currentTime
      let minute = Math.floor(nowplay.currentTime/60);
      this.now_m = minute<10?'0'+minute:minute;
      let sec = Math.floor(nowplay.currentTime%60);
      this.now_s = sec<10?'0'+sec:sec; 
    },
    duration(){
      let nowplay = document.getElementById('nowplay');
      let minute = Math.floor(nowplay.duration/60);
      this.duration_m = minute<10?'0'+minute:minute;
      let sec = Math.floor(nowplay.duration%60);
      this.duration_s = sec<10?'0'+sec:sec;
    },
    drag(e){
      e.preventDefault();
      this.isDragging = true;
      document.addEventListener("mousemove", this.onDrag);
      document.addEventListener("mouseup", this.onStopDrag);
      console.log("按下滑鼠");
    },
    onDrag(e){
      let nowplay = document.getElementById('nowplay');
      let progCon = document.getElementById('progressContainer');
      let rect = progCon.getBoundingClientRect();
      let offsetX = Math.max(e.clientX-rect.left,0);
      offsetX = Math.min(offsetX,rect.width);
      let percent = (offsetX/rect.width)*100;
      document.getElementById('progressBar').style.width = percent+"%";
      document.getElementById('progressHandle').style.left = percent+"%";
      nowplay.currentTime = (nowplay.duration*percent)/100;
      console.log(nowplay.duration*percent);
      console.log(percent);

    },
    onStopDrag(){
      console.log("放開滑鼠");
      this.isDragging = false;
      document.removeEventListener("mousemove", this.onDrag);
      document.removeEventListener("mouseup", this.onStopDrag);
    }
  }


}
</script>

<style scoped>  
.container{
  background-color: #E5DBD1;
  border-radius: 10px;
  box-shadow: 7px 7px 10px 1px #2F2E3C;
}
.player-panel{
  /* border: 1px solid red; */
  height: 100%;
  width: 370px;
  margin: 0 auto;
  padding-top: 40px;
}
.player-panel img{
  width: 100%;
  height: 360px;
  border-radius: 5px;
  object-fit: contain;
  /* border: red solid 5px; */
}
.player-panel button{
  width: 50px;
  height: 50px;
  border-radius: 100px;
  background-color: #2F2E3C;
  color: #F4EFE9;
  box-shadow: 1px 1px 3px #2F2E3C;
  cursor: pointer;
  margin-right: 10px;
  position: relative;
  /* border: 1px solid red; */
}
@media screen and (max-width:1320px){
  .player-panel button{
    width: 40px;
    height: 40px;
  }
}
.player-panel button>span{
  /* border: 1px solid blue; */
  position: absolute;
  width: 28%;
  height: 28%;
  top: 28%;
  left: 36%;
}
.ctrl-panel{
  display: flex;
  padding-bottom: 10px;
}
input.volume{
  width: 100px;
}
.volume-icon{
  width: 20px;
  margin-left: 5px;
}
.progress{
  display: flex;
  justify-content: space-between;
  align-items: center;
} 
.progress-container {
    width: 75%;
    height: 6px;
    background-color: #B3AEA8;
    position: relative;
    border-radius: 5px;
    cursor: pointer;
}

.progress-bar {
    height: 100%;
    width: 0%;
    background-color: #323443;
    border-radius: 5px;
}

.progress-handle {
    width: 10px;
    height: 10px;
    background-color: #1E1B38;
    /* border: 2px solid #fff8f8; */
    border-radius: 50%;
    position: absolute;
    top: 50%;
    transform: translate(-50%, -50%);
    /* cursor: pointer; */
}

</style>