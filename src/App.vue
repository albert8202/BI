
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

                <el-dropdown size="medium" split-button type="primary" style="margin-top: 2px"@command="handleCommand">
                  跳数：{{this.jumpNum}}
                  <el-dropdown-menu slot="dropdown">
                    <el-dropdown-item command="1">1</el-dropdown-item>
                    <el-dropdown-item command="2">2</el-dropdown-item>
                    <el-dropdown-item command="3">3</el-dropdown-item>
                    <el-dropdown-item command="4">4</el-dropdown-item>
                  </el-dropdown-menu>
                </el-dropdown>
                <el-form-item style="margin-left: 60px;">
                  <el-button type="primary" @click="onSubmit2">查询</el-button>
                </el-form-item>
              </el-form>
            </div>
          </div>
          <div >
            <div id="myChart" :style="{width: '1400px', height: '800px',margin:'auto'} " v-show="this.asideClick.singleQuery==true"></div>
            <el-pagination
              v-show="this.isPaginationShow1==true"
              layout="prev, pager, next"
              :total="1000"
              align="center">
            </el-pagination>
          </div>
          <div>
            <div id="myChart2" :style="{width: '1400px', height: '800px',margin:'auto'} " v-show="this.asideClick.doubleQuery==true" ></div>
            <el-pagination
              v-show="this.isPaginationShow2==true"
              layout="prev, pager, next"
              :total="1000"
              align="center">
            </el-pagination>
          </div>

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
      isPaginationShow1:false,
      isPaginationShow2:false,
      query1:{
        entity:''
      },
      query2: {
        entity1: '',
        entity2: ''
      },
      jumpNum:0,
      searched1:'',
      searched21:'',
      searched22:'',

      singleOption:{
        tooltip: {},
        animationDurationUpdate: function(idx) {
          // 越往后的数据延迟越大
          return idx * 100;
        },
        animationEasingUpdate: 'bounceIn',
        lineStyle: {
          width: 5,
          curveness: 0.2
        },
        series: [{
          name: 'Relation',
          type: 'graph',
          layout: 'force',
          label:
            {
              show:true,                  //是否显示标签。
              position:"inside"
            },
          roam: false,
          force:{
            layoutAnimation: true,
            repulsion: 7000
          },
          focusNodeAdjacency: true,
          edgeSymbol:['circle', 'arrow'],
          edgeSymbolSize:[20,35],
          data: [],
          links:[]
        }],
      },
      doubleOption:{
        tooltip: {},
        animationDurationUpdate: function(idx) {
          // 越往后的数据延迟越大
          return idx * 100;
        },
        animationEasingUpdate: 'bounceIn',
        lineStyle: {
          width: 5,
          curveness: 0.2
        },
        series: [{
          name: 'Relation',
          type: 'graph',
          layout: 'force',
          label:
            {
              show:true,                  //是否显示标签。
              position:"inside"
            },
          roam: true,
          force:{
            layoutAnimation: false,
            repulsion: 7000
          },
          focusNodeAdjacency: true,
          edgeSymbol:['circle', 'arrow'],
          edgeSymbolSize:[20,35],
          data: [],
          links:[]
        }],
      }
    }
  },
  watch: {
    //观察option的变化
    // singleOption: {
    //   handler(newVal, oldVal) {
    //     console.log("bian")
    //     if (this.myChart) {
    //       if (newVal) {
    //         this.myChart.setOption(newVal);
    //       } else {
    //         this.myChart.setOption(oldVal);
    //       }
    //     } else {
    //       console.log("else")
    //     }
    //   },
    //   deep: true //对象内部属性的监听，关键。
    // }
  },
  mounted(){

  },

  methods:{
    clickSingle(){
      this.asideClick.singleQuery=this.asideClick.singleQuery===false?true:false
      this.asideClick.doubleQuery=false
      this.isPaginationShow1=false
      this.isPaginationShow2=false

      this.asideClick.doubleQuery==false
    },
    clickDouble(){
      this.asideClick.doubleQuery=this.asideClick.doubleQuery===false?true:false
      this.asideClick.singleQuery=false
      this.isPaginationShow1=false
      this.isPaginationShow2=false
      this.asideClick.singleQuery==false


    },

    onSubmit1(){
      if (this.query1.entity==''){
        this.$message({
          message:'请先输入查询的实体',
          type:'warning'
        })
      }
      else {

        this.readJSON1(this.query1.entity,0,100);
      }
    },
    onSubmit2(){
      if (this.query2.entity1==''||this.query2.entity2==''){
        this.$message({
          message:'请先输入查询的实体',
          type:'warning'
        })
      }
      if (this.jumpNum==0){
        this.$message({
          message:'请选择跳数',
          type:'warning'
        })
      }
      else{
        this.readJSON2(this.query2.entity1,this.query2.entity2,20,this.jumpNum,0);
      }
    },
    handleCommand(_command){
      this.jumpNum=Number(_command);
      console.log(this.jumpNum);
    },

    async readJSON1(_nodeName,_start,_limit){

      var myChart = this.$echarts.init(document.getElementById('myChart'));
      myChart.setOption(this.singleOption);

      var req="http://yzchnb.xicp.io:28102/query/getSingleLinksByNamePageable"+'/'+_nodeName+'/'+_start+'/'+_limit;
      console.log(req);
        let res = await this.axios.get(req);
        var d = res.data.data.links;
        this.searched1 = res.data.data.name;
        var id=[]
          // console.log(d);
          // console.log(d[1]);
          // console.log(typeof d);
          var nodesData=[];
          var nodesLink=[];
          var nodeData = {
            name: this.searched1,
            value: 1606682,
            draggable: true,
            symbolSize: 100,
            itemStyle: {
              normal: {
                borderColor: 'rgb(27, 94, 93)',
                borderWidth: 40,
                shadowBlur: 122200,
                shadowColor: 'rgb(27, 94, 93)',
                color: 'rgb(27, 94, 93)'
              }
            }
          };
          nodesData.push(nodeData);
          var size = d.length;
          for (let i = 0; i < size; i++){
            var tempData={};
            var tempLink={};
            //找不到
            if (id.indexOf(d[i].n)==-1){
              id.push(d[i].n)
              tempData.name=d[i].n;
              tempData.draggable=true
              tempData.value=d[i].u;
              tempData.symbolSize=30;
              tempData.itemStyle={
                normal: {
                  borderColor: 'rgb(27, 94, 93)',
                  borderWidth: 40,
                  shadowBlur: 122200,
                  shadowColor: 'rgb(27, 94, 93)',
                  color: 'rgb(27, 94, 93)'
                }
              }
              nodesData.push(tempData);
            }

            if (d[i].d === -1) {
              tempLink.source=d[i].n;
              tempLink.target=this.searched1;
            }else {
              tempLink.source=this.searched1;
              tempLink.target=d[i].n;
            }
            tempLink.name=i;
            tempLink.des=d[i].r;
            nodesLink.push(tempLink);
          }

          this.singleOption.series[0].data = nodesData;
          this.singleOption.series[0].links = nodesLink;
          this.singleOption.title= { text: 'Single Search of '+`${this.query1.entity}` }
          myChart.setOption(this.singleOption);
          this.isPaginationShow1=true;
    },


    //_skip跳过前面多少结点
    async readJSON2(_node1,_node2,_limit,_jump,_skip){

        var myChart = this.$echarts.init(document.getElementById('myChart2'));
        myChart.setOption(this.doubleOption);
        var req ='http://yzchnb.xicp.io:28102/query/getPathsByTwoNodes';
        var res = await this.axios.get(req,{
          params:{
            endNodeName:_node2,
            limit_num:_limit,
            max_jump_num:_jump,
            skip_num:_skip,
            startNodeName:_node1
          }
        });
      console.log(res);
      var d = res.data.data.nodes;
        var nodesData=[];
        var nodesLink=[];
        //nodes
        var create_node=(name)=>{
          var tempData={};
          tempData.name=name;
          tempData.draggable=true
          // tempData.value=d[i].u;
          tempData.symbolSize=30;
          if (name==="Jack_Ma") {
            console.log("aaa");
            console.log(this.query2.entity1);
            console.log(this.query2.entity2);
          }
          if (name===this.query2.entity1){
            tempData.itemStyle={
              normal: {
                borderColor: 'rgb(27, 20, 93)',
                borderWidth: 40,
                shadowBlur: 122200,
                shadowColor: 'rgb(27, 20, 93)',
                color: 'rgb(27, 20, 93)'
              }
            }
          }
          else if(name===this.query2.entity2){
            tempData.itemStyle={
              normal: {
                borderColor: 'rgb(100, 200, 93)',
                borderWidth: 40,
                shadowBlur: 122200,
                shadowColor: 'rgb(100, 200, 93)',
                color: 'rgb(100, 200, 93)'
              }
            }
          }
          else {
            tempData.itemStyle={
              normal: {
                borderColor: 'rgb(27, 94, 93)',
                borderWidth: 40,
                shadowBlur: 122200,
                shadowColor: 'rgb(27, 94, 93)',
                color: 'rgb(27, 94, 93)'
              }
            };
          }
          return tempData
        }
        //links

        var name_set=new Set()
        console.log(d)
        for (var index2 in d){
          //遍历结点
          name_set.add(d[index2].n)
          if(d[index2].links){
            //有出边
            var linkList = d[index2].links;
            var size = linkList.length;
            //测试
            //遍历边
            for (var j=0;j<size;j++){
              var tempLink={};
              name_set.add(linkList[j].n)
              tempLink.source=d[index2].n;
              tempLink.target=linkList[j].n;
              nodesLink.push(tempLink);
            }
          }
        }
        for(var name of name_set){
          nodesData.push(create_node(name))
        }
        console.log(nodesData)
        this.doubleOption.series[0].data=nodesData;
        this.doubleOption.series[0].links=nodesLink;
        this.doubleOption.title={text:'Double Search of '+`${this.query2.entity1}`+' and '+`${this.query2.entity2}`}
        myChart.setOption(this.doubleOption);
        this.isPaginationShow2=true;
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
