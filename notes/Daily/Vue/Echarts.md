## echarts饼图数据为0时，如何不显示
```javascript
series: [
  {
    label:{
      normal:{
        formatter:function(e){
          let data=e.data;
          if(data.value==0){
            data.labelLine.show=false;
            data.label.show=false;
          }else{
            return  `${data.value}\n${e.percent}%`
          }
        },    
      }       
    },
    type: 'pie',
    radius: '65%',
    center: ['50%', '50%'],
    selectedMode: 'single',
    data: [
      {value: 123,name: '红色区域',itemStyle:{'color':'#74a275'},labelLine: {show: true},label:{show:true}},
      {value: 321, name: '黄色区域',itemStyle:{'color':'#769a9f'},labelLine: {show: true},label:{show:true}},
      {value: 666, name: '紫色区域',itemStyle:{'color':'#eddc7e'},labelLine: {show: true},label:{show:true}},
      {value: 333, name: '蓝色区域',itemStyle:{'color':'#e87e58'},labelLine: {show: true},label:{show:true}},
      {value: 111, name: '绿色区域',itemStyle:{'color':'#db6c69'},labelLine: {show: true},label:{show:true}}
    ],
    emphasis: {
      itemStyle: {
        shadowBlur: 10,
        shadowOffsetX: 0,
        shadowColor: 'rgba(0, 0, 0, 0.5)'
      }
    } 
  }
]
```