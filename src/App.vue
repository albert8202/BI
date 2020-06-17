<template>
  <div id="app">
    <el-container>
      <el-aside width="200px" style="height: 1000px">
        <!--<span>Dashboard</span>-->
        <el-menu
          class="el-menu-vertical-demo"
          text-color="grey"
          active-text-color="#000"
          style="font-weight: bold">
          <el-submenu index="1">
            <template slot="title">
              <i class="el-icon-location"></i>
              <span style="font-size: 18px">基本查询</span>
            </template>
            <el-menu-item-group >
              <el-menu-item index="1-1" style="font-size: 15px" v-on:click="clickSingle()">单体查询</el-menu-item>
              <el-menu-item index="1-2" style="font-size: 15px" v-on:click="clickDouble()">双体查询</el-menu-item>
            </el-menu-item-group>
          </el-submenu>
          <el-menu-item index="2">
            <i class="el-icon-menu"></i>
            <span slot="title" style="font-size: 18px">智能分析</span>
          </el-menu-item>
        </el-menu>
      </el-aside>

      <el-container>
        <el-header>
          <el-menu class="el-menu-demo" mode="horizontal" >
            <div class="dashboard">Dashboard</div>
          </el-menu>
        </el-header>
        <el-main>
          <div>

            <div class="query-single-entity"v-show="this.asideClick.singleQuery==true">
              <el-form :inline="true" :model="query1" class="demo-form-inline">
                <el-form-item label="实体">
                  <el-input v-model="query1.entity" placeholder="输入查询的实体"></el-input>
                </el-form-item>
                <el-form-item>
                  <el-button type="primary" @click="onSubmit1">查询</el-button>
                </el-form-item>
              </el-form>
            </div>

            <div class="query-two-entity"v-show="this.asideClick.doubleQuery==true">
              <el-form :inline="true" :model="query2" class="demo-form-inline">
                <el-form-item label="实体1">
                  <el-input v-model="query2.entity1" placeholder="输入查询的实体1"></el-input>
                </el-form-item>
                <el-form-item label="实体2">
                  <el-input v-model="query2.entity2" placeholder="输入查询的实体2"></el-input>
                </el-form-item>
                <el-form-item>
                  <el-button type="primary" @click="onSubmit2">查询</el-button>
                </el-form-item>
              </el-form>
            </div>
          </div>

          <div id="myChart" :style="{width: '1000px', height: '800px',margin:'auto'}"></div>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
export default {
  name: 'App',
  data(){
    return{
      asideClick: {
        singleQuery:false,
        doubleQuery:false
      },
      query1:{
        entity:''
      },
      query2: {
        entity1: '',
        entity2: ''
      },

      singleOption:{
        title: { text: 'Single Search' },
        tooltip: {},
        lineStyle: {
          width: 5,
          curveness: 0.2
        },
        series: [{
          name: '关系',
          type: 'graph',
          focusNodeAdjacency: true,
          edgeSymbol:['circle', 'arrow'],
          data: [],
          links:[]
        }],
        data:[],
        links:[]
      }

    }
  },
  watch: {
    //观察option的变化
    singleOption: {
      handler(newVal, oldVal) {
        console.log("bian")
        if (this.myChart) {
          if (newVal) {
            this.myChart.setOption(newVal);
          } else {
            this.myChart.setOption(oldVal);
          }
        } else {
          console.log("else")
        }
      },
      deep: true //对象内部属性的监听，关键。
    }
  },
  mounted(){
    this.readJSON1()
    let myChart = this.$echarts.init(document.getElementById('myChart'))
  },

  methods:{
    clickSingle(){
      this.asideClick.singleQuery=this.asideClick.singleQuery===false?true:false
      this.asideClick.doubleQuery=false
    },
    clickDouble(){
      this.asideClick.doubleQuery=this.asideClick.doubleQuery===false?true:false
      this.asideClick.singleQuery=false

    },

    onSubmit1(){
      if (this.query1.entity==''){
        this.$message({
          message:'请先输入查询的实体',
          type:'warning'
        })
      }
    },
    onSubmit2(){
      if (this.query2.entity1==''||this.query2.entity2==''){
        this.$message({
          message:'请先输入查询的实体',
          type:'warning'
        })
      }
    },
    drawLine(){
      // 基于准备好的dom，初始化echarts实例
      let myChart = this.$echarts.init(document.getElementById('myChart'))

      myChart.setOption({
        title: { text: 'test' },
        tooltip: {},
        lineStyle: {
          width: 5,
          curveness: 0.2
      },
        series: [{
          name: '销量',
          type: 'graph',
          focusNodeAdjacency: true,
          edgeSymbol:['circle', 'arrow'],
          data: [

            {
              name: '1',
              x: 10,
              y: 10,
              value: 10
            }, {
              name: '2',
              x: 100,
              y: 100,
              value: 20,
              symbolSize: 20,
              itemStyle: {
                normal: {
                  color: 'red'
                }
              }
            }
          ],
          links:[
            {
              source: '1',
              target: '2'
            }, {
              source: '2',
              target: '3'
            }
          ]

        }]
      });
    },
    async readJSON1(){
      var myChart = this.$echarts.init(document.getElementById('myChart'));
      singleOption:{}
      myChart.setOption(this.singleOption,true);
        let res = await this.axios.get('http://localhost:8080/static/SingleLinks.json');
        var d = res.data.data.links;
        var searched = res.data.data.name;
          // console.log(d);
          // console.log(d[1]);
          // console.log(typeof d);
          var nodesData=[];
          var nodesLink=[];
          var nodeData = {
            id:0,
            name: searched,
            x: 10,
            y: 10,
            value: 10
          };
          nodesData.push(nodeData);
          var size = d.length;
          for (let i = 0; i < size; i++){
            var tempData={};
            var tempLink={};
            tempData.id=i+1;
            tempData.name=d[i].n;
            // console.log(tempData);
            tempData.x=10+i;
            tempData.y=10+i;
            tempData.value=d[i].r;
            // console.log(typeof d[i].d);
            if (d[i].d === -1) {
              tempLink.source=d[i].n;
              tempLink.target=searched;
            }else {
              tempLink.source=searched;
              tempLink.target=d[i].n;
            }
            tempLink.name=i;
            tempLink.des=d[i].r;
            console.log(tempLink);

            nodesData.push(tempData);
            nodesLink.push(tempLink);
          }
          // console.log(nodesLink);
          // console.log(nodesData);
          this.singleOption.data=nodesData;
          this.singleOption.links=nodesLink;


          myChart.setOption(this.singleOption);

    },
    readJSON2(){
      this.axios.get('http://localhost:8080/static/test2.json').then(res=>{
        console.log(res.data.data[0].nodes)
        let linkedGraph = res.data.data[0]

      })
    }


  }
}
</script>

<style>
.dashboard{
  font-size: 40px;
  font-weight: bold;
  font-family:  "Segoe UI Semibold";
}
</style>
