<template>
    <div>
        <input type="file" name="" v-on:change="inputChange($event)">
        <img v-bind:src = "compressAfter" />
    </div>
</template>
<script>
  export default {
    data() {
      return {
        compressAfter: ''
      }
    },
    methods:{
      inputChange(event){
        var files = event.target.files,
          file = files[0]

        this.promiseFR(file)
          .then((base64Data)=>{
          return this.createImg(base64Data)
        })
      .then((img)=>{
          this.compressAfter = this.drawCanvas(img)
        var blob = this.base2Blob(this.compressAfter)
        this.upload(blob)
      })

      },
      // 获取reader对象 base64
      promiseFR(file){
        var reader = new FileReader()

        return new Promise((resolve,reject)=>{
          // console.log(this)
          reader.onload = function(event) {
          console.log(event.target)
          var base64Data = event.target.result
          resolve(base64Data)
        }
        reader.readAsDataURL(file)
      })
      },
      // 创建img base64 => Image
      createImg(base64Data){
        var img = new Image()

        return new Promise((resolve,reject)=>{

          img.onload = function () {
          var img = this
          resolve(img)

        }
        img.setAttribute('crossOrigin', 'anonymous')
        img.src = base64Data
      })
      },
      // canvas 压缩 Image => base64
      drawCanvas(img){

        var width = img.width,
          height = img.height,
          canvas = document.createElement('canvas'),
          ctx = canvas.getContext('2d')

        canvas.width = width
        canvas.height = height
        ctx.fillStyle = "#fff"
        ctx.fillRect(0, 0, width, height)
        ctx.drawImage(img, 0, 0, width, height)
        // console.log(canvas.toDataURL('image/jpeg', 0.1))
        return canvas.toDataURL('image/jpeg', 0.1)

      },
      // base64 => Blob
      base2Blob(base64){
        var ab = window.atob(base64.split(',')[1])
        var buffer = new ArrayBuffer(ab.length)
        var u8array = new Uint8Array(buffer)

        for(var i=0;i<ab.length;i++){
          u8array[i] = ab.charCodeAt(i)
        }

        var blob = new Blob([u8array.buffer],{type:'image/jpeg'})

        return blob

      },
      // formData 上传
      upload(file){
        var formdata = new FormData()

        formdata.append('fileName',file)
        this.$http.post('http://localhost:5000/upload',formdata)
          .then((res)=>{
          console.log('res : ',res)
      },(rej)=>{
          console.log('rej : ',rej)
        })
      }
    }
  }
</script>