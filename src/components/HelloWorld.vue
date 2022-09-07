<template>
  <div>
    <div slot="header" class="header">
      <div class="category-header">
        <span>昨日花费占比</span>
      </div>
    </div>
    <div ref="charts" class="charts"></div>
  </div>
</template>

<script>
import echarts from 'echarts';
export default {
  name: 'HelloWorld',
  data() {
    return {
      summerData: 0,
      picOptions: {
        title: {
          text: '昨日',
          subtext: 0,
          x: 'center',
          y: 'center',
          textVerticalAlign: 'auto',
          itemGap: 18,
          textStyle: {
            color: '#ccc',
            fontSize: 13,
            align: 'center',
            verticalAlign: 'middle',
          },
          subtextStyle: {
            color: '#000',
            fontSize: 17,
          },
        },
        tooltip: {
          //鼠标放上去的浮框效果
          show: false,
          // trigger: 'item',
        },
        legend: {
          //图例
          orient: 'vertical',
          top: '18%',
          left: '65%',
          itemGap: 40,
          itemWidth: 16, //图形宽度
          itemHeight: 16, //图形高度
          formatter: this.formatterLegend,
          textStyle: {
            rich: {
              name: {
                fontSize: 14,
                color: '#ccc',
                padding: [0, 10],
              },
              percentage: {
                fontSize: 14,
                color: '#000',
                padding: [0, 10],
              },
              value: {
                fontSize: 14,
                color: '#000',
                padding: [0, 10],
              },
            },
          },
        },
        series: {
          data: [
            { name: '00:00-06:00', percentage: '11%', value: 138508.67 },
            { name: '06:00-12:00', percentage: '23%', value: 293517.32 },
            { name: '12:00-18:00', percentage: '26%', value: 331333.81 },
            { name: '18:00-24:00', percentage: '37%', value: 467787.09 },
          ],
          type: 'pie',
          radius: ['50%', '70%'],
          avoidLabelOverlap: false,
          itemStyle: {
            borderRadius: 10,
            borderColor: '#fff',
            borderWidth: 0,
            normal: {
              color: function (colors) {
                //饼条颜色
                var colorList = ['#0080ff', '#83FFE6', '#FEEB51', '#8B88FF'];
                return colorList[colors.dataIndex];
              },
            },
          },
          emphasis: {
            //高亮的效果
            label: {
              show: false,
            },
          },
          label: {
            show: false,
          },
          labelLine: {
            show: false,
          },
          value: {
            fontSize: 32,
            fontWeight: 'bold',
            color: '#333333',
          },
        },
      },
    };
  },
  methods: {
    formatterLegend(name) {
      //图例
      let value, percentage;
      for (let i = 0; i < this.picOptions.series.data.length; i++) {
        if (this.picOptions.series.data[i].name === name) {
          value = this.picOptions.series.data[i].value;
          percentage = this.picOptions.series.data[i].percentage;
        }
      }
      return `{name|${name}}{percentage|${percentage}}{value|${value} ¥}`;
    },
  },
  mounted() {
    const mycharts = echarts.init(this.$refs.charts);
    //价格之和
    this.summerData = this.picOptions.series.data.reduce((a, b) => {
      return (Number(a) + Number(b.value)).toFixed(2);
    }, 0);
    this.picOptions.title.subtext = `${this.summerData}¥`;

    mycharts.setOption(this.picOptions);
    // 绑定事件
    mycharts.on('mouseover', (params) => {
      // console.log(1)
      const { value } = params.data; // 解构鼠标移上去的数据
      // 重新设置标题
      mycharts.setOption({
        title: {
          text: '昨日',
          subtext: `${value}¥`,
        },
      });
    });
    mycharts.on('mouseout', () => {
      mycharts.setOption({
        title: {
          text: '昨日',
          subtext: `${this.summerData}¥`,
        },
      });
    });
  },
};
</script>

<style scoped>
.category-header {
  display: flex;
  justify-content: center;
  align-items: center;
}
span {
  margin-right: 600px;
}
.charts {
  width: 100%;
  height: 300px;
}
</style>
