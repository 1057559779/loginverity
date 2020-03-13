<template>
    <el-popover
            placement="top"
            width="335"
            v-model="visible">
        <!--    验证滑块-->
        <div style="width: auto">
            <div class="bigbox">
                <!--真图片-->
                <canvas width="310" height="155" id="canvas" ref="canvas"></canvas>
                <!--拼图滑块-->
                <canvas width="310" ref="block" id="block"></canvas>
                <!--拉动轨道-->
                <div class="pathway">
                    <div class="pathwayText">拖动滑块完成拼图
                        <div class="pathwayBtn" id="pathwayBtn" ref="pathwayBtn" @mouseup="mouseup"></div>
                    </div>
                </div>
            </div>
        </div>
    </el-popover>
</template>

<script>
    export default {
        name: "verified",
        props:{
            visible:{
                type:Boolean,
                value:false
            }
        },
        data(){
            return{
                img: document.createElement('img'),
                url:"http://120.55.88.202:8082/oneimage/04fe4119ac89985817afbcef46bf0d9fd3f531b8.jpg@1100w_484h_1c_100q.webp.jpg",
                basedata:"",
            }
        },methods:{
             getImage(url){
                 this.$axios.get("sendBase64Image",{
                     params:{
                         imageUrl:url
                     }
                 }).then((res)=>{
                     this.basedata=res.data
                     this.doit()
                 })
             },
            successLogin(){
                this.$emit("successHandle")
            },
            mouseup(){
                var box_x = 160, y = 40, w = 42, r = 10, PI = Math.PI
                let block = this.$refs.block
                let pathwayBtn=this.$refs.pathwayBtn
                //当鼠标抬起时，清除这两个事件
                document.onmouseleave=document.onmousemove=document.onmouseup=null;
                let nownum = parseInt(pathwayBtn.style.left.split("px")[0])
                //box= 150
                if(nownum-17<box_x && nownum-3>box_x){
                    this.successLogin()
                    pathwayBtn.style.left="0px"
                    block.style.left="22px"
                }else {
                    // console.log("失败"+nownum+"_"+box_x)
                    pathwayBtn.style.left="0px"
                    block.style.left="22px"
                }
            },
            doit(){
                var box_x = 160, y = 40, w = 42, r = 10, PI = Math.PI
                let canvas = this.$refs.canvas
                let block = this.$refs.block
                var canvas_ctx = canvas .getContext('2d')
                var block_ctx =  block.getContext('2d')
                let bgImg = new Image();

                bgImg.src ='data:image/jpeg;base64,'+this.basedata
                bgImg.onload = function(){
                    canvas_ctx.drawImage(bgImg, 0, 0, 310, 155)
                    block_ctx.drawImage(bgImg, 0, 0, 310, 155)
                    var blockWidth = w + r * 2
                    var _y = y - r * 2 + 2 // 滑块实际的y坐标
                    var ImageData = block_ctx.getImageData(box_x, _y, blockWidth, blockWidth)
                    block.width = blockWidth
                    block_ctx.putImageData(ImageData, 0, _y)
                }
                //绘制
                draw(canvas_ctx, 'fill')
                draw(block_ctx, 'clip')

                //画拼图
                function draw(ctx, operation) {
                    //缺口
                    if(operation=='fill'){
                        ctx.beginPath()
                        // ctx.fillStyle = 'rgb(0,0,0)'
                        ctx.moveTo(box_x,y)
                        ctx.lineTo(box_x+w/2,y)
                        ctx.arc(box_x+w/2,y-r+2, r,0,2*PI)
                        ctx.lineTo(box_x+w/2,y)
                        ctx.lineTo(box_x+w,y)
                        ctx.lineTo(box_x+w,y+w/2)
                        ctx.arc(box_x+w+r-2,y+w/2,r,0,2*PI)
                        ctx.lineTo(box_x+w,y+w/2)
                        ctx.lineTo(box_x+w,y+w)
                        ctx.lineTo(box_x,y+w)
                        ctx.lineTo(box_x,y)
                        ctx.fill()
                        ctx.beginPath()
                        ctx.arc(box_x,y+w/2, r,1.5*PI,0.5*PI)
                        ctx.globalCompositeOperation = "xor"
                        ctx.fill()
                    }else {
                        //拼图
                        ctx.beginPath()
                        ctx.moveTo(box_x,y)
                        ctx.lineTo(box_x+w/2,y)
                        ctx.arc(box_x+w/2,y-r+2, r,0,2*PI)
                        ctx.lineTo(box_x+w/2,y)
                        ctx.lineTo(box_x+w,y)
                        ctx.lineTo(box_x+w,y+w/2)
                        ctx.arc(box_x+w+r-2,y+w/2,r,0,2*PI)
                        ctx.lineTo(box_x+w,y+w/2)
                        ctx.lineTo(box_x+w,y+w)
                        ctx.lineTo(box_x,y+w)
                        ctx.lineTo(box_x,y)
                        ctx.clip()
                        ctx.beginPath()
                        ctx.arc(box_x,y+w/2, r,1.5*PI,0.5*PI)
                        ctx.globalCompositeOperation = "xor"
                        ctx.fill()
                    }

                }
                let pathwayBtn=this.$refs.pathwayBtn
                //移动
                //鼠标按下事件
                pathwayBtn.onmousedown=function(ev){
                    var ev=window.event||ev;
                    //屏幕的可视宽高   作控制拖拽范围用
                    var cW=document.documentElement.clientWidth;
                    //元素的占位宽
                    var bW=pathwayBtn.offsetWidth;
                    //计算鼠标按下的位置
                    var disX=ev.clientX-pathwayBtn.offsetLeft;
                    //添加移动事件
                    document.onmousemove =function(ev){
                        var ev=window.event||ev;
                        //计算鼠标移动的距离
                        var x=ev.clientX-disX;
                        //判断可拖拽的范围，限定在屏幕可视区域
                        if(x<21){
                            pathwayBtn.style.left='0px';
                            //cW-bW计算的是box的left属性可设置的最大值，大于它的时候就会超出可视区域
                        }else{
                            if(x<260){
                                pathwayBtn.style.left=x-12+'px';
                                block.style.left=x+'px';
                                let nownum = parseInt(pathwayBtn.style.left.split("px")[0])
                            }
                        }
                    }
                }
            }
        },mounted() {
            let url="http://120.55.88.202:8082/oneimage/04fe4119ac89985817afbcef46bf0d9fd3f531b8.jpg@1100w_484h_1c_100q.webp.jpg"
            this.getImage(url)
        }
    }
</script>

<style scoped>
    .bigbox{
        padding: 10px;
        width: 310px;
    }
    #canvas{
        position: relative;
    }
    #block{
        position: absolute;
        left: 25px;
        top: 22px;
    }
    .pathway{
        position: relative;
        width: 100%;
        background-color: #DFE1E2;
        height: 40px;
        display: flex;
        justify-content: center;
        align-items: center;
    }
    .pathwayText{

    }
    .pathwayBtn{
        position: absolute;
        width: 50px;
        height: 50px;
        background-color: #F9F9F9;
        border-radius:50px;
        left: 0px;
        top: -5px;
    }
</style>