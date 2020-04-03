<template>
  <div id="app">
    <button @click="onClick">点我</button>
    <!-- <virtual-list
      :size="80"
      :remain="8"
      :items="items"
      :variable="true"
    >
      <Item 
        slot-scope="{item}" 
        :item="item"/>
    </virtual-list> -->
    <div  style="100%; border: 1px solid #ccc;height: 100px;">
      <!-- <img src="./static/023.jpg" alt="" style="width: 500px;"> -->
      <canvas id="canvas" style="width: 100%">

      </canvas>
    </div>
  </div>
</template>
<script>
import VirtualList from './components/virtual-list-copy';
import axios from 'axios';
import Item from './components/item';
import Mock from 'mockjs'
let items = [];
let imgList = [];
const src = 'https://dahoutai.side.vip/undefined/12fbe0e9337a8a936390530e3d3d454f.jpg'
for(let i = 0 ; i<20 ; i++){
  items.push({id:i,value:Mock.Random.sentence()});
  // imgList.push( i%2 === 0 ?src : 'https://s1.ax1x.com/2020/03/26/GpUHk8.jpg')
  imgList.push(src)
}

export default {
  name: 'App',
  data(){
    return {
      items, 
      url:null, 
      canDrawW: null,
      c:null,
      zanDoneH: null
    }
  },
  components:{
    VirtualList,
    Item
  },
  methods: {
    onClick (c){
      axios.post('http://49.233.180.52:3000/users/update', 
      {
        name: '修改过55',
        age: 45,
        id: 5
      }).then((res) =>{
          console.log(res)
        })
        .catch(function (error) {
          console.log(error);
        });
          // this.url = this.c.toDataURL("image/jpeg",1);
          // let a = document.createElement("a");
          // a.href = this.url;
          // a.setAttribute("download", 'canvas');
          // a.click();
    },
    initCanvas () {
      const c = document.getElementById('canvas')
      this.c = c
      const scale = window.devicePixelRatio;
      const w = window.screen.width
      this.canDrawW = w - 20
      const H = window.screen.height
      const aW = scale * w
      c.height = scale * H;
      c.width =  aW;
      // console.log('w', w, scale * w, 'H',scale * H, H, scale)
      const ctx = c.getContext('2d')
      ctx.scale(scale, scale)
      ctx.fillStyle = "#ffffff";
      const img = new Image()
      img.src = require('./static/023.jpg')
      let cImgH
      img.onload = () => {
        cImgH = img.height*(w/img.width)
        // console.log(cImgH,  'img')
        ctx.fillRect(0,0,aW,c.height);
        ctx.drawImage(img, 0, 0, w, cImgH)
        ctx.font = "14px 微软雅黑";
        let text = []
        for (let i=0; i<4;i++) {
          text.push({
            name: Mock.Random.cword(2,4) + ',',
            des: Mock.Random.csentence(1,10)
          })
        }
        this.drawTime(ctx, 10,  cImgH + 24)
        this.drawLove(ctx, 15, cImgH + 34, 14, 14)
        // this.drawBack(ctx, text, 14, cImgH + 34, this.canDrawW)
        this.drawLove(ctx,15, cImgH + 54)
        // this.drawZan(ctx, text, 14, 34, cImgH + 54)
        this.drawRoundRect(ctx, 5, 34, cImgH + 34, 30, 30,)
      }
    },
    drawImage() {

    },
    drawBack(ctx, text, fontSize, startH, width) {
      ctx.fillStyle = "#F5F5F5";
      let oneCount = 0
      let line = 1
      const count = Math.round(width / fontSize)
      // console.log(count,'count')
      text.map(v => {
        const name = v.name
        if(oneCount+name.length >= count) {
          line ++
          // console.log(oneCount+name.length,line,'line')
          oneCount = name.length + 2
        } else {
          oneCount+=name.length
        }
      })
      // console.log(line, 'totalCount')
      const drawH = line*fontSize + line * 7
      ctx.fillRect(10, startH, width, drawH +fontSize)
    },
    drawLove(ctx, drawW, drawH, w, h) {
      ctx.fillStyle = "#4A6189";
      const img = new Image()
      img.src = require('./static/爱心.png')
      img.onload = () => { 
        ctx.drawImage(img, drawW, drawH + 8, w, h)
      }
    },
    drawZan(ctx, list, fontSize, startW, startH) {
      ctx.fillStyle = "#4A6189";
      let drawW = startW
      let drawWH = startH
      list.map((v,i) =>{
        let len = v.name.length
        const isSameLine = this.canDrawW - (len-1) * fontSize - drawW < 0
        if (isSameLine) {
          drawW = startW
          drawWH = drawWH + fontSize + 7
        }
        ctx.fillText(v.name, drawW, drawWH)
        drawW = drawW + len * fontSize
        this.zanDoneH = drawW
      })
    },
    drawTime(ctx, drawW, drawH) {
      ctx.fillStyle = "#ccc";
      ctx.fillText('2小时前', 10, drawH)
    },
    drawBtn(ctx, drawW, drawH) {
      ctx.fillStyle = "#ccc";
      ctx.fillRect('2小时前', 10, cImgH + 24)
    },
		drawRoundRect (ctx,r,initX,initY,w,h) {
      ctx.fillStyle = "#e9e9e9";
      let x = initX
      let y = initY
      let space = 5
      let canDrawW = this.canDrawW - 20
      let remain = canDrawW % (w +10)
      let count = Math.floor(this.canDrawW / (w + space))
      while(remain > space) {
        ++space
        remain = this.canDrawW % (w + space)
        count = Math.floor(canDrawW / (w +space))
      }
      imgList.forEach((v,i) => {
        let img = new Image()
        img.src = v
        let line = 0
        img.onload = () => {
          if(i%count === 0 ) {
            x = initX
            y =  initY + Math.floor(i/count) * (5 + h)
            console.log(y, 'yyyy')
          } else {
            x = initX + (w+space) * (i%count)
            // console.log(x, i,i%count, 'xxx')
          }
          if (w < 2 * r) r = w / 2
          if (h < 2 * r) r = h / 2
          ctx.beginPath()
          ctx.save()
          ctx.moveTo(x+r, y)
          ctx.arcTo(x+w, y, x+w, y+h, r)
          ctx.arcTo(x+w, y+h, x, y+h, r)
          ctx.arcTo(x, y+h, x, y, r)
          ctx.arcTo(x, y, x+w, y, r)
          ctx.closePath();
          ctx.clip()
          ctx.drawImage(img, x, y, w, h)
          ctx.restore()           
        }
      })

    },
    loadImage(url) {
      return new Promise((resolve, reject) => {
        const img =  new Image()
        img.src = url
        img.setAttribute('crossOrigin', 'anonymous');
        img.onload = () =>{
          resolve(img)
        }
        img.onerror = () => {
          reject(img)
        }
      })
    }
  },
  mounted() {
    this.initCanvas()
  }
}
</script>
<style lang="stylus">
* {
  margin 0; 
  padding:0;
  box-sizing: border-box;
}
</style>
