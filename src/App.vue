
<template>
  <div id="app">
    <el-container>
      <el-aside width="200px" style="height: 1000px">
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

      <el-container>
        <el-header>
          <el-menu class="el-menu-demo" mode="horizontal">
            <div class="dashboard">Dashboard</div>
          </el-menu>
        </el-header>
        <el-main>
          <div>
            <div class="query-single-entity" v-show="this.asideClick.singleQuery==true">
              <el-form :inline="true" :model="query1" class="demo-form-inline">
                <el-form-item label="实体">
                  <el-input v-model="query1.entity" placeholder="输入查询的实体"></el-input>
                </el-form-item>
                <el-form-item>
                  <el-button type="primary" @click="onSubmit1">查询</el-button>
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
              </el-form>
            </div>
            <div class="query-two-entity"v-show="this.asideClick.analysis==true">
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
            <div align="center" style="margin-bottom: 4px" v-show="this.isPaginationShow1==true">
              <el-button-group >
                <el-button type="primary" icon="el-icon-arrow-left" @click="onPrev1">上一页</el-button>
                <el-button type="primary"@click="onNext1">下一页<i class="el-icon-arrow-right el-icon--right"></i></el-button>
              </el-button-group>
            </div>
            <div
              id="myChart"
              :style="{width: '1100px', height: '600px',margin:'auto', border: '1px solid grey'} "
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
            <div align="center" style="margin-bottom: 4px" v-show="this.isPaginationShow2==true">
              <el-button-group >
                <el-button type="primary" icon="el-icon-arrow-left"@click="onPrev2">上一页</el-button>
                <el-button type="primary"@click="onNext2">下一页<i class="el-icon-arrow-right el-icon--right"></i></el-button>
              </el-button-group>
            </div>
            <div
              id="myChart2"
              :style="{width: '1100px', height: '600px',margin:'auto', border: '1px solid grey'} "
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
              <div id="myChart3" :style="{width: '600px', height: '600px',margin:'auto'} " align="center"></div>
              <div align="center">Jaccard相似度</div>
            </div>
            <div >
              <div id="myChart4" :style="{width: '600px', height: '600px',margin:'auto'} " align="center"></div>
              <div align="center">Cosine相似度</div>
            </div>
          </div>

        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      host:'http://yzchnb.xicp.io:28102/',
      asideClick: {
        singleQuery: false,
        doubleQuery: false,
        analysis:false
      },
      isPaginationShow1: false,
      isPaginationShow2: false,
      query1: {
        entity: "",
        limitNum:50,
        current:0
      },
      query2: {
        entity1: "",
        entity2: "",
        limitNum:50,
        current:0
      },
      query3:{
        entity1:'',
        entity1:''
      },
      jumpNum: 0,
      searched1: "",
      searched21: "",
      searched22: "",


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
            name_set: new Set(),
            name_set2: new Set(),
            myChart: null,
            myChart2: null
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
    this.myChart = this.$echarts.init(document.getElementById("myChart"));
    this.myChart.on("dblclick", { dataType: "node" }, params => {
      this.update_data(this.myChart, params.name,this.name_set);
    });
    this.myChart2 = this.$echarts.init(document.getElementById("myChart2"));
    this.myChart2.on("dblclick", { dataType: "node" }, params => {
      this.update_data(this.myChart2, params.name, this.name_set2);
    });
  },

  methods: {
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
    update_data(chart, name, name_set) {
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
      this.axios.get(req).then(res => {
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
      });

    },
    clickSingle() {
      this.asideClick.singleQuery=this.asideClick.singleQuery===false?true:false;
      this.asideClick.doubleQuery=false;
      this.isPaginationShow1=false;
      this.isPaginationShow2=false;
      this.asideClick.analysis=false;

      this.asideClick.doubleQuery == false;
    },
    clickDouble() {
      this.asideClick.doubleQuery=this.asideClick.doubleQuery===false?true:false;
      this.asideClick.singleQuery=false;
      this.isPaginationShow1=false;
      this.isPaginationShow2=false;
      this.asideClick.analysis=false;
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

    onSubmit1() {
      if (this.query1.entity == "") {
        this.$message({
          message: "请先输入查询的实体",
          type: "warning"
        });
      } else {
        this.readJSON1(this.query1.entity, 0, 100);
        this.query1.current+=100;
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
          20,
          this.jumpNum,
          0
        );
        this.query2.current+=20;
      }
    },

    //分页
    onNext1(){
      console.log("query1 next");
      this.readJSON1(this.query1.entity,this.query1.current,this.query1.limitNum);
      this.query1.current+=this.query1.limitNum;
    },

    onPrev1(){
      console.log("query1 previous");

      this.query1.current=this.query1.current-this.query1.limitNum < 0 ? 0:this.query1.current-this.query1.limitNum;
      this.readJSON1(this.query1.entity,this.query1.current,this.query1.limitNum);

    },
    onNext2(){
      console.log("query2 next");
      this.readJSON2(this.query2.entity1,this.query2.entity2,this.query2.limitNum,this.jumpNum, this.query2.current);
      this.query2.current+=this.query2.limitNum;
    },
    onPrev2(){
      console.log("query2 previous");
      this.query2.current = this.query2.current-this.query2.limitNum<0 ? 0 : this.query2.current-this.query2.limitNum;
      this.readJSON2(this.query2.entity1,this.query2.entity2,this.query2.limitNum,this.jumpNum, this.query2.current);
    },

    handleCommand(_command) {
      this.jumpNum = Number(_command);
      console.log(this.jumpNum);
    },

    async readJSON1(_nodeName, _start, _limit) {
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
      let res = await this.axios.get(req);
      console.log("cha1")
      console.log(res)
      var d = res.data.data.links;
      this.searched1 = res.data.data.name;
      var id = [];
      // console.log(d);
      // console.log(d[1]);
      // console.log(typeof d);
      var nodesData = [];
      var nodesLink = [];
      var nodeData = this.create_node0(this.searched1);
      this.name_set = new Set();
      nodesData.push(nodeData);
      this.name_set.add(nodeData.name);
      var size = d.length;
      for (let i = 0; i < size; i++) {
        var tempData = {};
        var tempLink = {};
        //找不到
        if (id.indexOf(d[i].n) == -1) {
          id.push(d[i].n);
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
      myChart.setOption(this.singleOption);
      this.isPaginationShow1 = true;
    },

    //_skip跳过前面多少结点
    async readJSON2(_node1, _node2, _limit, _jump, _skip) {
      var myChart = this.myChart2;
      myChart.setOption(this.doubleOption);
      var req = this.host+"query/getPathsByTwoNodes";

      var res = await this.axios.get(req, {
        params: {
          endNodeName: _node2,
          limit_num: _limit,
          max_jump_num: _jump,
          skip_num: _skip,
          startNodeName: _node1
        }
      });
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
      for (var index2 in d) {
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
      this.isPaginationShow2 = true;
    },
    async readJSON3(_name1,_name2){
      var req = this.host+'query/getSimilarityByTwoNodes';
      var res = await this.axios(req,{
        params:{
          name1:_name1,
          name2:_name2
        }
      });

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
