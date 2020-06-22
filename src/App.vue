
<template>
  <div id="app" :style="{height: height + 'px', width: width + 'px'}">
    <el-container>
      <el-aside :style="{width: 0.15 * width, height: '100%'}">
        <!--<span>Dashboard</span>-->
        <el-menu
          class="el-menu-vertical-demo"
          text-color="grey"
          active-text-color="#000"
          style="font-weight: bold"
        >
          <el-submenu index="1">
            <template slot="title">
              <i class="el-icon-location"></i>
              <span style="font-size: 18px">基本查询</span>
            </template>
            <el-menu-item-group>
              <el-menu-item index="1-1" style="font-size: 15px" v-on:click="clickSingle()">单体查询</el-menu-item>
              <el-menu-item index="1-2" style="font-size: 15px" v-on:click="clickDouble()">双体查询</el-menu-item>
            </el-menu-item-group>
          </el-submenu>
          <el-menu-item index="2" v-on:click="clickAnalysis()">
            <i class="el-icon-menu"></i>
            <span slot="title" style="font-size: 18px">智能分析</span>
          </el-menu-item>
        </el-menu>
      </el-aside>

      <el-container style="height: 100%; width: 85%;">
        <el-header id="Dashboard">
          <el-menu class="el-menu-demo" mode="horizontal">
            <div class="dashboard">Dashboard</div>
          </el-menu>
        </el-header>
        <el-main>
          <div id='input'>
            <div class="query-single-entity" v-show="this.asideClick.singleQuery==true">
              <el-form :inline="true" :model="query1" class="demo-form-inline">
                <el-form-item label="实体">
                  <el-input v-model="query1.entity" placeholder="输入查询的实体"></el-input>
                </el-form-item>
                <el-form-item>
                  <el-button type="primary" @click="onSubmit1">查询</el-button>
                </el-form-item>
                <el-form-item v-show="this.isPaginationShow1==true">
                  <el-button type="primary" icon="el-icon-arrow-left" @click="onPrev1">上一页</el-button>
                  <span>{{this.query1.current.start}} ~ {{this.query1.current.end}}</span>
                  <el-button type="primary" @click="onNext1">下一页<i class="el-icon-arrow-right el-icon--right"></i></el-button>
                </el-form-item>

              </el-form>
            </div>

            <div class="query-two-entity" v-show="this.asideClick.doubleQuery==true">
              <el-form :inline="true" :model="query2" class="demo-form-inline">
                <el-form-item label="实体1">
                  <el-input v-model="query2.entity1" placeholder="输入查询的实体1"></el-input>
                </el-form-item>
                <el-form-item label="实体2">
                  <el-input v-model="query2.entity2" placeholder="输入查询的实体2"></el-input>
                </el-form-item>


                <el-dropdown
                  size="medium"
                  split-button
                  type="primary"
                  style="margin-top: 2px"
                  @command="handleCommand"
                >
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
                <el-form-item v-show="this.isPaginationShow2==true">
                  <el-button type="primary" icon="el-icon-arrow-left" @click="onPrev2">上一页</el-button>
                  <span>{{this.query2.current.start}} ~ {{this.query2.current.end}}</span>
                  <el-button type="primary" @click="onNext2">下一页<i class="el-icon-arrow-right el-icon--right"></i></el-button>
                </el-form-item>
              </el-form>
            </div>
            <div class="query-two-entity" v-show="this.asideClick.analysis==true">
              <el-form :inline="true" :model="query3" class="demo-form-inline">
                <el-form-item label="实体1">
                  <el-input v-model="query3.entity1" placeholder="输入查询的实体1"></el-input>
                </el-form-item>
                <el-form-item label="实体2">
                  <el-input v-model="query3.entity2" placeholder="输入查询的实体2"></el-input>
                </el-form-item>

                <el-form-item style="margin-left: 60px;">
                  <el-button type="primary" @click="onSubmit3">查询</el-button>
                </el-form-item>
              </el-form>
            </div>

          </div>



          <div>
            <div
              v-loading="loading"
              id="myChart"
              :style="{width: canvasWidth, height: canvasHeight, margin:'auto', border: '1px solid grey'} "
              v-show="this.asideClick.singleQuery==true"
            ></div>
            <!--<el-pagination-->
              <!--v-show="this.isPaginationShow1==true"-->
              <!--layout="prev, pager, next"-->
              <!--:total="1000"-->
              <!--align="center"-->
            <!--&gt;</el-pagination>-->
          </div>

          <div>
            <!--<div align="center" style="margin-bottom: 4px" v-show="this.isPaginationShow2==true">-->
              <!--<el-button-group >-->
                <!--<el-button type="primary" icon="el-icon-arrow-left"@click="onPrev2">上一页</el-button>-->
                <!--<el-button type="primary"@click="onNext2">下一页<i class="el-icon-arrow-right el-icon&#45;&#45;right"></i></el-button>-->
              <!--</el-button-group>-->
            <!--</div>-->
            <div
              v-loading="loading"
              id="myChart2"
              :style="{width: canvasWidth, height: canvasHeight, margin:'auto', border: '1px solid grey'}"
              v-show="this.asideClick.doubleQuery==true"
            ></div>
            <!--<el-pagination-->
              <!--v-show="this.isPaginationShow2==true"-->
              <!--layout="prev, pager, next"-->
              <!--:total="1000"-->
              <!--align="center"-->
            <!--&gt;</el-pagination>-->


          </div>
          <div style="display: flex" v-show="this.asideClick.analysis==true">
            <div >
              <div v-loading="loading" id="myChart3" :style="{width: simChartWidth, height: canvasHeight, margin:'auto'} " align="center"></div>
              <div align="center">Jaccard相似度</div>
            </div>
            <div >
              <div v-loading="loading" id="myChart4" :style="{width: simChartWidth, height: canvasHeight, margin:'auto'} " align="center"></div>
              <div align="center">Cosine相似度</div>
            </div>
          </div>

        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
import VUE from 'vue'
export default {
  name: "App",
  data() {
    return {
      host:'http://yzchnb.xicp.io:28102/',
      height: `${document.documentElement.clientHeight}`,
      width: `${document.documentElement.clientWidth}`,
      loading: false,
      canvasHeight: '',
      canvasWidth: '',
      simChartWidth: '',
      currResizeableChart: null,
      asideClick: {
        singleQuery: false,
        doubleQuery: false,
        analysis:false
      },
      isPaginationShow1: false,
      isPaginationShow2: false,
      query1: {
        entity: "",
        limitNum:20,
        current:{
          start:0,
          end:0
        },
        page:0
      },
      query2: {
        entity1: "",
        entity2: "",
        limitNum:20,
        current:{
          start:0,
          end:0
        },
        page:0
      },
      query3:{
        entity1:'',
        entity1:''
      },
      jumpNum: 0,
      searched1: "",
      searched21: "",
      searched22: "",
      name_set: new Set(),
      name_set2: new Set(),
      myChart: null,
      myChart2: null,

      singleOption: {
        tooltip: {},
        animationDurationUpdate: function(idx) {
          // 越往后的数据延迟越大
          return idx * 100;
        },
        animationEasingUpdate: "bounceIn",
        lineStyle: {
          width: 5,
          curveness: 0.2
        },
        series: [
          {
            name: "Relation",
            type: "graph",
            layout: "force",
            label: {
              show: true, //是否显示标签。
              position: "inside"
            },
            edgeLabel: {
              normal: {
                show: true,
                textStyle: {
                  fontSize: 13
                },
                formatter(x) {
                  return x.data.name;
                }
              }
            },
            roam: true,
            force: {
              edgeLength: 120,
              repulsion: 1000
            },
            focusNodeAdjacency: true,
            edgeSymbol: ["", "arrow"],
            edgeSymbolSize: [10, 35],
            symbolSize: 29,
            data: [],
            links: [],
          }
        ]
      },
      
      doubleOption: {
        tooltip: {},
        animationDurationUpdate: function(idx) {
          // 越往后的数据延迟越大
          return idx * 100;
        },
        animationEasingUpdate: "bounceIn",
        lineStyle: {
          width: 5,
          curveness: 0.2
        },
        series: [
          {
            name: "Relation",
            type: "graph",
            layout: "force",
            label: {
              show: true, //是否显示标签
              position: "inside"
            },
            edgeLabel: {
              normal: {
                show: true,
                textStyle: {
                  fontSize: 13
                },
                formatter(x) {
                  return x.data.name;
                }
              }
            },
            roam: true,
            force: {
              edgeLength: 120,
              repulsion: 2000
            },
            focusNodeAdjacency: true,
            edgeSymbol: ["", "arrow"],
            edgeSymbolSize: [10, 35],
            symbolSize: 29,
            data: [],
            links: []
          }
        ]
      },
      analyseOptions:{
        jOption:{
          tooltip: {},
          series:[
            {
              name: 'jaccard',
              type: 'gauge',
              detail: {formatter: '{value}%'},
              data: [{value: 50, name: 'Jaccard相似度'}]
            }
          ]
        },
        cOption:{
          tooltip: {},
          series:[
            {
              name: 'cosine',
              type: 'gauge',
              detail: {formatter: '{value}%'},
              data: [{value:50, name: 'Cosine相似度'}]
            }
          ]
        }
      }
    };
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
  mounted() {
    var _this = this;
    document.getElementById("input").offsetHeight;
    this.myChart = this.$echarts.init(document.getElementById("myChart"));
    this.myChart.on("dblclick", { dataType: "node" }, params => {
      this.update_data(this.myChart, params.name,this.name_set);
    });
    this.myChart2 = this.$echarts.init(document.getElementById("myChart2"));
    this.myChart2.on("dblclick", { dataType: "node" }, params => {
      this.update_data(this.myChart2, params.name, this.name_set2);
    });
    _this.reset_canvas_size();
    window.onresize = () => {
      _this.reset_canvas_size();
    }
  },

  methods: {
    reset_canvas_size(){
      var _this = this;
      var dashBoardHeight = document.getElementById("Dashboard").offsetHeight;
      var inputHeight = document.getElementById("input").offsetHeight;
      _this.height = `${document.documentElement.clientHeight}`;
      _this.width  = `${document.documentElement.clientWidth}`;
      _this.canvasHeight = `${_this.height - dashBoardHeight - inputHeight - 100}px`;
      _this.simChartWidth = `${(0.85 * _this.width) / 2}px`
      _this.canvasWidth = `${0.85 * _this.width - 90}px`;
      VUE.nextTick(() => {
        if(_this.asideClick.singleQuery && _this.myChart){
          _this.myChart.resize();
        }else if(_this.asideClick.doubleQuery && _this.myChart2){
          _this.myChart2.resize();
        }
      });
    },
    create_node0(name) {
      var tempData = {};
      tempData.name = name;
      tempData.draggable = true;
      tempData.symbolSize = 30;
      tempData.itemStyle = {
        normal: {
          borderColor: "rgb(27, 94, 93)",
          borderWidth: 40,
          shadowBlur: 122200,
          shadowColor: "rgb(27, 94, 93)",
          color: "rgb(27, 94, 93)"
        }
      };
      return tempData;
    },
    create_node(name) {
      var tempData = {};
      tempData.name = name;
      tempData.draggable = true;
      // tempData.value=d[i].u;
      tempData.symbolSize = 30;
      if (name === this.query2.entity1) {
        tempData.itemStyle = {
          normal: {
            borderColor: "rgb(27, 20, 93)",
            borderWidth: 40,
            shadowBlur: 122200,
            shadowColor: "rgb(27, 20, 93)",
            color: "rgb(27, 20, 93)"
          }
        };
      } else if (name === this.query2.entity2) {
        tempData.itemStyle = {
          normal: {
            borderColor: "rgb(100, 200, 93)",
            borderWidth: 40,
            shadowBlur: 122200,
            shadowColor: "rgb(100, 200, 93)",
            color: "rgb(100, 200, 93)"
          }
        };
      } else {
        tempData.itemStyle = {
          normal: {
            borderColor: "rgb(27, 94, 93)",
            borderWidth: 40,
            shadowBlur: 122200,
            shadowColor: "rgb(27, 94, 93)",
            color: "rgb(27, 94, 93)"
          }
        };
      }
      return tempData;
    },
    async update_data(chart, name, name_set) {
      var option = chart.getOption();
      var search_func = (links, source, target) => {
        for (var i = 0; i < links.length; ++i) {
          if (source == links[i].source && target == links[i].target) {
            return true;
          }
        }
        return false;
      };
      var req =
        "http://yzchnb.xicp.io:28102/query/getSingleLinksByNamePageable" +
        "/" +
        name +
        "/" +
        0 +
        "/" +
        5;
      var create_node=this.create_node0
      var _this = this;
      this.loading = true;
      var res = null;
      try{
        res = await this.axios.get(req);
        this.loading = false;
      }catch(e){
        _this.loading = false;
        _this.showError();
        return;
      }      
      var processRes = function(res){
        
        if(res.data.code !== 0){
          _this.showNodeNotExists();
          return;
        }
        var d = res.data.data.links;
        var size = d.length;
        var nodesLink=option.series[0].links
        var nodesData=option.series[0].data
        console.log(name,name_set)
        for (let i = 0; i < size; i++) {
          var tempData = {};
          var tempLink = {};
          //找不到

          if (!name_set.has(d[i].n)) {
            name_set.add(d[i].n);
            tempData = create_node(d[i].n);
            console.log(tempData)
            nodesData.push(tempData);
          }
          var flag;
          if (d[i].d === -1) {
            flag=search_func(nodesLink,d[i].n,name)
            tempLink.source = d[i].n;
            tempLink.target = name;
          } else {
            flag=search_func(nodesLink,name,d[i].n)
            tempLink.source = name;
            tempLink.target = d[i].n;
          }
          console.log(flag,tempLink)
          if(flag){
            continue
          }
          tempLink.name = d[i].r;
          // tempLink.des = d[i].r;
          nodesLink.push(tempLink);
        }
        chart.setOption(option)
      }
      processRes(res);
    },
    clickSingle() {
      this.asideClick.singleQuery=this.asideClick.singleQuery===false?true:false;
      this.asideClick.doubleQuery=false;
      this.isPaginationShow1=false;
      this.isPaginationShow2=false;
      this.asideClick.analysis=false;
      this.asideClick.doubleQuery = false;
      VUE.nextTick(()=>{
        this.reset_canvas_size();
      });
    },
    clickDouble() {
      this.asideClick.doubleQuery=this.asideClick.doubleQuery===false?true:false;
      this.asideClick.singleQuery=false;
      this.isPaginationShow1=false;
      this.isPaginationShow2=false;
      this.asideClick.analysis=false;
      VUE.nextTick(()=>{
        this.reset_canvas_size();
      });
    },

    clickAnalysis(){
      console.log(this.asideClick)
      this.asideClick.analysis=this.asideClick.analysis===false?true:false
      this.asideClick.singleQuery=false
      this.asideClick.doubleQuery=false
      this.isPaginationShow1=false
      this.isPaginationShow2=false

    },
    onSubmit3(){
      console.log("clicked");
      if(this.query3.entity1==''||this.query3.entity2==''){
        this.$message({
          message:'请输入查询的实体',
          type:'warning'
        })

      }
      else{
        this.readJSON3(this.query3.entity1,this.query3.entity2);

      }

    },

    async onSubmit1() {
      if (this.query1.entity == "") {
        this.$message({
          message: "请先输入查询的实体",
          type: "warning"
        });
      } else {
        await this.readJSON1(this.query1.entity, 0, this.query1.limitNum);
        this.query1.current.start = 0;
        this.query1.current.end = this.query1.limitNum;
        this.query1.page = 1;
      }
    },
    onSubmit2() {
      if (this.query2.entity1 == "" || this.query2.entity2 == "") {
        this.$message({
          message: "请先输入查询的实体",
          type: "warning"
        });
      }
      if (this.jumpNum == 0) {
        this.$message({
          message: "请选择跳数",
          type: "warning"
        });
      } else {
        this.readJSON2(
          this.query2.entity1,
          this.query2.entity2,
          this.query2.limitNum,
          this.jumpNum,
          0
        );
        this.reset_canvas_size();
        this.query2.current.start = 0;
        this.query2.current.end = this.query2.limitNum;        
        this.query2.page = 1;
      }
    },

    //分页
    async onNext1(){
      console.log("query1 next");
      var hasMore = await this.readJSON1(this.query1.entity,this.query1.current.end,this.query1.limitNum);
      if(!hasMore){
        console.log("show?")
        this.showNoMoreNodes();
        return;
      }
      this.query1.current.start=this.query1.current.end
      this.query1.current.end+=this.query1.limitNum
      this.query1.page+=1;

    },

    onPrev1(){
      // console.log("query1 previous");
      // console.log(this.query1.current)
      if(this.query1.current.start === 0){
        return;
      }
      this.query1.current.start=(this.query1.current.start-this.query1.limitNum) < 0 ? 0:(this.query1.current.start-this.query1.limitNum);
      this.readJSON1(this.query1.entity,this.query1.current.start,this.query1.limitNum);
      // console.log(this.query1.current.start, this.query1.current.end,typeof this.query1.limitNum);

      this.query1.current.end -= this.query1.limitNum;

      if (this.query1.current.start==0){
        this.query1.page=1;
      }
      else this.query1.page-=1;


  },
    async onNext2(){
      console.log("query2 next");
      var hasMore = await this.readJSON2(this.query2.entity1,this.query2.entity2,this.query2.limitNum,this.jumpNum, this.query2.current.end);
      if(!hasMore){
        this.showNoMoreNodes();
        return;
      }
      this.query2.current.start = this.query2.current.end;
      this.query2.current.end += this.query2.limitNum;
      this.query2.page+=1;
    },
    onPrev2(){
      if(this.query2.current.start === 0){
        return;
      }
      this.query2.current.start = this.query2.current.start-this.query2.limitNum<0 ? 0 : this.query2.current.start-this.query2.limitNum;
      this.readJSON2(this.query2.entity1,this.query2.entity2,this.query2.limitNum,this.jumpNum, this.query2.current.start);
      this.query2.current.end -= this.query2.limitNum;
      console.log(this.query2.current.start)
      if (this.query2.current.start==0){
        this.query2.page=1;
      }
      else this.query2.page-=1;
    },

    handleCommand(_command) {
      this.jumpNum = Number(_command);
      console.log(this.jumpNum);
    },

    async readJSON1(_nodeName, _start, _limit) {
      var _this = this;
      this.reset_canvas_size();
      var myChart = this.$echarts.init(document.getElementById("myChart"));
      myChart.setOption(this.singleOption);
      myChart.on('mouseup',function(params){
        var option=myChart.getOption();
        option.series[0].data[params.dataIndex].x=params.event.offsetX;
        option.series[0].data[params.dataIndex].y=params.event.offsetY;
        option.series[0].data[params.dataIndex].fixed=true;
        myChart.setOption(option);
      });

      var req =
        this.host+
        "query/getSingleLinksByNamePageable" +
        "/" +
        _nodeName +
        "/" +
        _start +
        "/" +
        _limit;
      console.log(req);
      this.loading = true;
      var res = null;
      try{
        res = await this.axios.get(req);
      }catch(e){
        _this.loading = false;
        _this.showError();
        return;
      }
      console.log("test", res);
      this.loading = false;
      var _this = this;
      if(res.data.code !== 0){
        _this.showNodeNotExists();
        return false;
      }
      console.log("cha1", res)
      var d = res.data.data.links;
      if(!d || d.length === 0){
        return false;
      }
      this.searched1 = res.data.data.name;
      // console.log(d);
      // console.log(d[1]);
      // console.log(typeof d);
      var nodesData = [];
      var nodesLink = [];
      var nodeData = this.create_node0(this.searched1);
      //中心点，将位置固定，并且更换颜色
      nodeData.itemStyle.normal.color = "rgb(100, 100, 100)";
      nodeData.itemStyle.normal.shadowColor = "rgb(100, 100, 100)";
      nodeData.itemStyle.normal.borderColor = "rgb(100, 100, 100)";
      this.name_set = new Set();
      nodesData.push(nodeData);
      this.name_set.add(nodeData.name);
      var size = d.length;
      for (let i = 0; i < size; i++) {
        var tempData = {};
        var tempLink = {};
        //找不到
        if (!this.name_set.has(d[i].n)) {
          this.name_set.add(d[i].n);
          tempData = this.create_node0(d[i].n);
          nodesData.push(tempData);
        }

        if (d[i].d === -1) {
          tempLink.source = d[i].n;
          tempLink.target = this.searched1;
        } else {
          tempLink.source = this.searched1;
          tempLink.target = d[i].n;
        }
        tempLink.name=d[i].r;

        nodesLink.push(tempLink);
      }

      this.singleOption.series[0].data = nodesData;
      this.singleOption.series[0].links = nodesLink;
      this.singleOption.title = {
        text: "Single Search of " + `${this.query1.entity}`
      };
      // console.log(this.singleOption.series[0].links)
      await myChart.setOption(this.singleOption);
      nodeData.fixed = true;
      await myChart.setOption(this.singleOption);
      this.isPaginationShow1 = true;
      return true;
    },

    //_skip跳过前面多少结点
    async readJSON2(_node1, _node2, _limit, _jump, _skip) {
      var _this = this;
      var myChart = this.myChart2;
      myChart.setOption(this.doubleOption);
      myChart.on('mouseup',function(params){
        var option=myChart.getOption();
        option.series[0].data[params.dataIndex].x=params.event.offsetX;
        option.series[0].data[params.dataIndex].y=params.event.offsetY;
        option.series[0].data[params.dataIndex].fixed=true;
        myChart.setOption(option);
      });
      var req = this.host+"query/getPathsByTwoNodes";
      this.loading = true;
      var res = null;
      try{
        res = await this.axios.get(req, {
        params: {
          endNodeName: _node2,
          limit_num: _limit,
          max_jump_num: _jump,
          skip_num: _skip,
          startNodeName: _node1
        }
      });
      }catch(e){
        _this.loading = false;
        _this.showError();
        return false;
      }
      this.loading = false;
      var _this = this;
      if(res.data.code !== 0){
        _this.showNodeNotExists();
        return false;
      }
      console.log("aaa")
      console.log(res)
      var d = res.data.data.nodes;
      var nodesData = [];
      var nodesLink = [];
      //nodes
      var create_node = this.create_node;
      //links

      this.name_set2 = new Set();
      console.log(d);
      var hasMore = false;
      for (var index2 in d) {
        hasMore = true;
        //遍历结点
        this.name_set2.add(d[index2].n);
        if (d[index2].links) {
          //有出边
          var linkList = d[index2].links;
          var size = linkList.length;
          //测试
          //遍历边
          for (var j = 0; j < size; j++) {
            var tempLink = {};
            this.name_set2.add(linkList[j].n);
            tempLink.source = d[index2].n;
            tempLink.target = linkList[j].n;
            tempLink.name = linkList[j].r;
            nodesLink.push(tempLink);
          }
        }
      }
      if(!hasMore){
        return false;
      }
      for (var name of this.name_set2) {
        nodesData.push(create_node(name));
      }
      console.log(nodesData);
      this.doubleOption.series[0].data = nodesData;
      this.doubleOption.series[0].links = nodesLink;
      this.doubleOption.title = {
        text:
          "Double Search of " +
          `${this.query2.entity1}` +
          " and " +
          `${this.query2.entity2}`
      };
      myChart.setOption(this.doubleOption);
      for(var d of nodesData){
        if(d.name === _node1 || d.name === _node2){
          d.fixed = true;
        }
      }
      myChart.setOption(this.doubleOption);
      this.isPaginationShow2 = true;
      return true;
    },
    async readJSON3(_name1,_name2){
      var req = this.host+'query/getSimilarityByTwoNodes';
      this.loading = true;
      var res = null;
      var _this = this;
      try{
        res = await this.axios(req,{
          params:{
            name1:_name1,
            name2:_name2
          }
        });
      }catch(e){
        _this.loading = false;
        _this.showError();
        return;
      }
      this.loading = false;
      var _this = this;
      if(res.data.code !== 0){
        _this.showNodeNotExists();
        return false;
      }

      console.log(res)
      var myChart1 = this.$echarts.init(document.getElementById('myChart3'));
      var myChart2 = this.$echarts.init(document.getElementById('myChart4'));
      myChart1.setOption(this.analyseOptions.jOption);
      myChart2.setOption(this.analyseOptions.cOption);
      this.analyseOptions.jOption.series[0].data[0].value = Number((res.data.data.Jaccard*100).toFixed(1));
      this.analyseOptions.cOption.series[0].data[0].value = Number((res.data.data.Cosine*100).toFixed(1));
      console.log( this.analyseOptions.jOption.series[0].data[0].value)
      console.log(this.analyseOptions.cOption.series[0].data[0].value)
      myChart1.setOption(this.analyseOptions.jOption);
      myChart2.setOption(this.analyseOptions.cOption);
      this.reset_canvas_size();
      return true;
    },
    showNodeNotExists(){
      this.$message({
          showClose: true,
          message: '节点不存在！',
          type: 'warning'
        });
    },
    showNoMoreNodes(){
      this.$message({
          showClose: true,
          message: '到头了',
          type: 'warning'
        });
    },
    showError(){
      this.$message({
          showClose: true,
          message: '出错了',
          type: 'warning'
        });
    }

  }
};
</script>

<style>
.dashboard {
  font-size: 40px;
  font-weight: bold;
  font-family: "Segoe UI Semibold";
}
</style>
