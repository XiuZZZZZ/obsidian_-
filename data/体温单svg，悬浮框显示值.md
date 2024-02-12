
# 修改

导入

Nur.NIS.Service.Chart.Base.Point

Nur.NIS.Service.Chart.DAO.Temperature  iteratorItemVals

Nur.NIS.Service.Chart.DAO.Temperature  generateLines

Nur.NIS.Service.Chart.DAO.Temperature  pointToLine

scripts/nurse/temperature/svg/svgUtils.js  drawPoint

scripts/nurse/hisui/singleEntry.js  formatDate


# 入口

```js
    points.forEach(function(point){
	    if(point.breakLineFlag!="Y"){
		    var key=point.x+"_"+point.y;
		    drawPoint(elem,point.icon,point.size/POINTRATE,point.x,point.y,point.color,"point_"+point.x+"_"+point.y,obj[key]);
	        i++;    
	    }
    });
```


# 画图标

```js
/**
 * 画图标
 */
 function drawPoint(draw,icon,size,x,y,color,id,point){
    var rowLeading=  '1.0em';
    var fontWeight = 'normal';    
    var text = draw.text(icon+"");       
    var anchor="start"
     
    //获取或设置font
    text.font({
         size: size,
         anchor: anchor, //设置位置的相对定位点
         leading: rowLeading,
         weight: fontWeight
     });
    var span = text.tspan(icon+"").fill(color).attr('id',"tspan"+id);   
    text.move(x-size/3,y-size/2);
    text.on("mouseover",function(){
	    $("#tooltip p:eq(0)").html(point.obs.join("，"));
	    $("#tooltip p:eq(1)").html("录入人："+point.user.join("，"));
	    $("#tooltip p:eq(2)").html("时间："+point.dtime);
		$("#tooltip").css({"top":(y+1)/A4H*100+"%","left":(x+1)/A4W*100+"%"}).show();    
	})
	text.on("mouseout",function(){
		$("#tooltip").hide();    
	})
 }
```