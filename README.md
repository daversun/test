<!doctype html>
<html>
<head>
<meta http-equiv="Pragma" content="no-cache" />
<meta http-equiv="Cache-Control" content="no-cache" />
<meta http-equiv="Expires" content="0" />
<meta http-equiv="Content-Type" content="text/html;charset=utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1.0,user-scalable=no" />
<style type="text/css">
canvas{
    position: absolute;
    left: 0;
    top: 0;
}
</style>
</head>
<body bgcolor="#000000">
<canvas id="tree"></canvas>
<canvas id="flower"></canvas>
<script>
//两个canvas
var tree = document.getElementById("tree");
tree.width = window.innerWidth;
tree.height = window.innerHeight ;
var tCxt = tree.getContext("2d");
var flower = document.getElementById("flower");
flower.width = window.innerWidth;
flower.height = window.innerHeight ;
var cxt = flower.getContext("2d");
 
var flowerList = [];//樱花列表
var rootTop = 450 ;//树起点
var flowerColor = "rgba(255,192,203,.3)" ;//花色
var flowerColorDeep = "rgba(241,158,194,.5)" ;//花色深
var treeColor2 = "rgba(255,192,203,.5)" ;//树枝颜色
var treeColor = "#FFF" ;//树干颜色
var fallList = [];//飘落樱花列表
var g = 0.01 ;//重力加速度
var gWind = 0.005;//风力加速度
var limitSpeedY = 1;//速度上限
var limitSpeedX = 1 ;//速度上限
 
cxt.shadowColor= "#FFF" ;
cxt.shadowBlur = 10 ;
 
function drawTree(x,y,deg,step,type){
    var deg1 = step%2 == 0 ? 0.1 : -0.1 ;
    var x1 = x + Math.cos(deg+deg1) * (step+4) * 0.8 ;//以步长来判断枝干长度 x轴偏移大一些
    var y1 = y + Math.sin(deg+deg1) * (step-1) * 0.8 ;//以步长来判断枝干长度 Y轴压缩一些
    tCxt.beginPath();
    tCxt.lineWidth = step/3;
    tCxt.moveTo(x,y);
    tCxt.lineTo(x1,y1);
    tCxt.strokeStyle = (step > 5 ) ? treeColor : treeColor2 ;//细纸条都换成花的颜色
    tCxt.stroke();
    if(step > 20){//树干相交的位置有间隙，以一个圆填充
        tCxt.fillStyle = treeColor ;
        tCxt.arc(x,y,step/6,0,Math.PI*2);
        tCxt.fill();
    }
    if(step < 3 || (step < 23 && Math.random() > 0.1)){
        //末梢位置 画花瓣
        var color = [flowerColorDeep,flowerColor,flowerColor][Math.round(Math.random()+0.2)] ;
        var r = 2+Math.random()*2
        tCxt.fillStyle = color ;
        tCxt.arc(x1+Math.random()*3,y1+Math.random()*3,r,0,Math.PI)
        tCxt.fill();
        flowerList.push({x:x,y:y,sx:(Math.random()-0.5),sy:0,color:color,r:r,deg:deg});//保存下画樱花的位置
    }
    step -- ;
    if(step > 0){
        drawTree(x1,y1,deg,step,type);
        if(step%3 == 1 && step > 0 && step < 30)
            drawTree(x1,y1,deg+0.2 + 0.3 * Math.random(),Math.round(step/1.13));//右分叉
        if(step%3 == 0 && step > 0 && step < 30)
            drawTree(x1,y1,deg-0.2 - 0.3 * Math.random(),Math.round(step/1.13));//左分叉
    }
}
 
drawTree(tree.width/2,rootTop,-Math.PI/2,30,1);//执行
 
var len = flowerList.length ;
function step(){
    if(Math.random() > 0.3)    fallList.push(flowerList[Math.floor(Math.random()*len)]);//随机取出一个，花瓣复制到飘落花瓣的列表中
 
    cxt.clearRect(0,0,tree.width,tree.height);
    for(var i = 0 ;i < fallList.length ; i ++){
        if(fallList[i].sy < limitSpeedY) fallList[i].sy += g ;
        fallList[i].sx += gWind ;
        fallList[i].x += fallList[i].sx ;
        fallList[i].y += fallList[i].sy ;
        if(fallList[i].y > rootTop){//飘到树根的花瓣移除
            fallList.splice(i,1);
            i -- ;
            continue ;
        }
        cxt.beginPath();
        cxt.fillStyle = fallList[i].color ;
        fallList[i].deg += fallList[i].sx*0.05 ;//跟速度相关的旋转花瓣
        cxt.arc(fallList[i].x,fallList[i].y,fallList[i].r,fallList[i].deg,fallList[i].deg+Math.PI*1.3);
        cxt.fill();
    }
    requestAnimationFrame(step);
}
requestAnimationFrame(step);
</script>
</body>
</html>
