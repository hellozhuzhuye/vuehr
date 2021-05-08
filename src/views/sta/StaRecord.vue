<template>
    <div>
      <div id="empEval" style="width: 500px ; height: 500px ; margin: 10px auto"></div>
      <div id="empApply" style="width: 500px ; height: 500px ; margin: 10px auto"></div>
      <div id="empSalary" style="width: 500px ; height: 500px ; margin: 10px auto"></div>
      <div id="empEdu" style="width: 500px ; height: 500px ; margin: 10px auto"></div>
    </div>
</template>

<script>
    export default {
        name: "StaRecord" ,
      data() {
        return {
          msg: 'Welcome to Your Vue.js App'
        }
      },
      mounted() {
        this.drawEmpEval();
        this.drawEmpApply();
        this.drawEmpSalary();
        this.drawEmpEdu();

      },
      methods: {
        drawEmpEval(){
          // 基于准备好的dom，初始化echarts实例
          let empEval = this.$echarts.init(document.getElementById('empEval'))
          // 绘制图表
          empEval.setOption({
            backgroundColor: '#ffffff',

            title: {
              text: '考勤统计',
              left: 'center',
              top: 20,
              // textStyle: {
              //   color: '#bd4848'
              // }
            },

            tooltip: {
              trigger: 'item'
            },

            visualMap: {
              show: false,
              min: 80,
              max: 600,
              inRange: {
                colorLightness: [0, 1]
              }
            },
            series: [
              {
                name: '访问来源',
                type: 'pie',
                radius: '55%',
                center: ['50%', '50%'],
                data: [
                  {value: 335, name: '旷工'},
                  {value: 310, name: '迟到'},
                  {value: 274, name: '早退'},
                  {value: 235, name: '请假'},
                  {value: 400, name: '正常'},
                  {value: 190, name: '出差'}
                ].sort(function (a, b) { return a.value - b.value; }),
                roseType: 'radius',
                label: {
                  color: 'rgba(0,0,0,0.3)'
                },
                labelLine: {
                  lineStyle: {
                    color: 'rgba(0,0,0,0.3)'
                  },
                  smooth: 0.2,
                  length: 10,
                  length2: 20
                },
                itemStyle: {
                  color: '#c23531',
                  shadowBlur: 200,
                  shadowColor: 'rgba(0, 0, 0, 0.5)'
                },

                animationType: 'scale',
                animationEasing: 'elasticOut',
                animationDelay: function (idx) {
                  return Math.random() * 200;
                }
              }
            ]
          });
        },
        drawEmpApply(){
          // 基于准备好的dom，初始化echarts实例
          let empApply = this.$echarts.init(document.getElementById('empApply'))
          // 绘制图表

          empApply.setOption(
              {
                title:{
                  text: '申请统计',
                  left: 'center'
                },
                tooltip: {
                  trigger: 'item'
                },
                legend: {
                  top: 'bottom'
                },
                series: [
                  {
                    name: '申请次数',
                    type: 'pie',
                    radius: ['40%', '70%'],
                    avoidLabelOverlap: false,
                    label: {
                      show: false,
                      position: 'center'
                    },
                    emphasis: {
                      label: {
                        show: true,
                        fontSize: '40',
                        fontWeight: 'bold'
                      }
                    },
                    labelLine: {
                      show: false
                    },
                    data: [
                      {value: 1048, name: '加班申请'},
                      {value: 735, name: '请假申请'},
                      {value: 580, name: '离职申请'},
                      {value: 484, name: '出差申请'},
                      {value: 300, name: '转正申请'},
                      {value: 300, name: '报销申请'}
                    ]
                  }
                ]
              }
          )
        },
        drawEmpSalary(){
          // 基于准备好的dom，初始化echarts实例
          let empSalary = this.$echarts.init(document.getElementById('empSalary'))
          // 绘制图表

          empSalary.setOption(
              {
                tooltip: {
                  trigger: 'axis',
                  axisPointer: {
                    type: 'cross',
                    crossStyle: {
                      color: '#999'
                    }
                  }
                },
                toolbox: {
                  feature: {
                    dataView: {show: true, readOnly: false},
                    magicType: {show: true, type: ['line', 'bar']},
                    restore: {show: true},
                    saveAsImage: {show: true}
                  }
                },
                legend: {
                  data: ['蒸发量', '降水量', '平均温度']
                },
                xAxis: [
                  {
                    type: 'category',
                    data: ['2010年', '2011年', '2012年', '2013年', '2014年', '2015年', '2016年', '2017年', '2018年', '2019年', '2020年'],
                    axisPointer: {
                      type: 'shadow'
                    }
                  }
                ],
                yAxis: [
                  {
                    type: 'value',
                    name: '在职人数',
                    min: 0,
                    max: 250,
                    interval: 50,
                    axisLabel: {
                      formatter: '{value} 人'
                    }
                  },
                  {
                    type: 'value',
                    name: '平均薪资',
                    min: 0,
                    max: 20000,
                    interval: 5000,
                    axisLabel: {
                      formatter: '{value} ￥'
                    }
                  }
                ],
                series: [
                  {
                    name: '在职人数',
                    type: 'bar',
                    data: [20, 30, 40, 50, 60, 70, 80, 90, 100, 120, 150, 195]
                  },
                  {
                    name: '平均薪资',
                    type: 'line',
                    yAxisIndex: 1,
                    data: [4500, 5000, 5600, 5900, 6300, 7000, 7500, 8030, 8800, 9350, 10500, 13000]
                  }
                ]
              }
          )
        },
        drawEmpEdu(){
          // 基于准备好的dom，初始化echarts实例
          let empEdu = this.$echarts.init(document.getElementById('empEdu'))
          // 绘制图表

          empEdu.setOption(
              {
                title: {
                  text: '员工学历统计',
                  left: 'center'
                },
                tooltip: {
                  trigger: 'item'
                },
                legend: {
                  orient: 'vertical',
                  left: 'left',
                },
                series: [
                  {
                    name: '访问来源',
                    type: 'pie',
                    radius: '50%',
                    data: [
                      {value: 120, name: '初中'},
                      {value: 220, name: '高中'},
                      {value: 350, name: '大专'},
                      {value: 484, name: '本科'},
                      {value: 30, name: '硕士'},
                      {value: 15, name: '博士'},
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
              }
          )
        }
      }
    }
</script>

<style scoped>

</style>