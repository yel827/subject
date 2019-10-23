<template>
  <div class="mHome">
    <div class="titleQ">
      <el-tabs v-model="activeName" @tab-click="handleClick">
        <!-- 选项卡一用户管理布局 -->
        <el-tab-pane label="主机监控" name="first">
          <div class="search">
            <!-- from表单 -->
            <el-form :inline="true" :model="formInline" class="demo-form-inline">
              <el-form-item label="主机名称" class="right">
                <el-input v-model="formInline.hostName" placeholder="主机名称"></el-input>
              </el-form-item>
              <el-form-item label="主机IP" class="right">
                <el-input v-model="formInline.hostIp" placeholder="主机IP"></el-input>
              </el-form-item>
              <el-form-item>
                <el-button type="primary" @click="onSubmit" class="right">搜索</el-button>
                <span class="icon_tu">
                  <span style="margin-right:20px;">
                    <i
                      class="el-icon-sunrise-1"
                      style="font-size:20px; margin-right:6px; color:#3584f3;"
                    ></i> 正常
                  </span>
                  <span>
                    <i
                      class="el-icon-sunrise-1"
                      style="font-size:20px; margin-right:6px; color:orange;"
                    ></i> 告警
                  </span>
                </span>
              </el-form-item>
            </el-form>
          </div>
          <!-- table表格结构 -->
          <el-table
            :data="tableData.slice((currentPage-1)*pagesize,currentPage*pagesize)"
            style="width: 100%;"
            class="tabP"
          >
            <el-table-column prop="id" label="ID" width="180"></el-table-column>
            <el-table-column prop="id" label="状态" width="180">
              <i class="el-icon-sunrise" style="font-size:20px; margin-right:6px; color:orange;"></i>
            </el-table-column>
            <el-table-column prop="hostName" label="主机名称"></el-table-column>
            <el-table-column prop="hostIp" label="主机IP"></el-table-column>
            <el-table-column prop="operationSystem" label="操作系统"></el-table-column>
            <el-table-column prop="name" label="CPU利用率">
              <el-progress :percentage="percentage" :color="customColors"></el-progress>
            </el-table-column>
            <el-table-column prop="name" label="内存利用率">
              <el-progress :percentage="percentage1" :color="customColor"></el-progress>
            </el-table-column>
            <el-table-column prop="createTime" label="更新时间"></el-table-column>

            <el-table-column label="操作">
              <template slot-scope="scope" ref='tabl'>
                <el-button type="text" @click="editgsForm(scope.row.hostIp)">
                  <i class="icon iconfont icon-chakan" style="font-size:18px; font-weight:bold;"></i>
                </el-button>
                
              </template>
            </el-table-column>
          </el-table>
        </el-tab-pane>
        <!-- 选项卡二配置管理布局 -->
        <el-tab-pane label="组件监控" name="second">
          <div class="search">
            <el-form :inline="true"  class="demo-form-inline">
              <span class="demonstration">选择组件</span>
              <el-select v-model="compName" placeholder="请选择" class="right">
                <el-option
                  v-for="item in options"
                  :key="item.id"
                  :label="item.name"
                  :value="item.name"
                ></el-option>
              </el-select>
              <span class="demonstration">组件状态</span>
              <el-select v-model="compStatus" placeholder="请选择" class="right">
                <el-option
                  v-for="item in optionss"
                  :key="item.name"
                  :label="item.name"
                  :value="item.value"
                ></el-option>
              </el-select>
              <el-form-item>
                <el-button type="primary" @click="onSubmit2" class="right">搜索</el-button>
              </el-form-item>
            </el-form>
          </div>
          <el-table
            :data="tableData2.slice((currentPage-1)*pagesize,currentPage*pagesize)"
            style="width: 100%;"
            class="tabP"
          >
            <el-table-column prop="id" label="组件ID" width="180"></el-table-column>
            <el-table-column prop="name" label="组件名称" width="180"></el-table-column>
            <el-table-column prop="totalComponents" label="组件数量"></el-table-column>
            <el-table-column prop="normalTotalComponents" label="正常组件数量"></el-table-column>
            <el-table-column prop="alarmTotalComponents" label="告警组件数量"></el-table-column>
            <el-table-column prop="updateTime" label="更新时间"></el-table-column>
            <!-- 组件监控操作详情 -->
            <el-table-column label="操作">  
              <template slot-scope="scope">
                <el-button type="text" @click="getDetail2(scope.$index, scope.row)">
                  <i class="icon iconfont icon-chakan" style="font-size:18px; font-weight:bold;"></i>
                </el-button>
                 <el-button type="text">
                  <i class="el-icon-refresh"  style="font-size:24px; font-weight:bold;"></i>
                 </el-button>
              <el-dialog 
                width='54%'
                z-index="99"
              title="组件监控详情" :visible.sync="dialogTableVisible">
              <span class="demonstration">组件状态</span>
              <el-select v-model="compStatus" placeholder="请选择" class="right">
                <el-option
                  v-for="item in optionss"
                  :key="item.name"
                  :label="item.name"
                  :value="item.value"
                ></el-option>
              </el-select>
              <el-button type="primary" class="right">搜索</el-button>
                <el-table :data="gridData">
                  <el-table-column property="hostIp" label="组件IP" width="100"></el-table-column>
                  <el-table-column property="port" label="组件端口" width="100"></el-table-column>
                  <el-table-column property="status" label="组件状态" width="100"></el-table-column>
                  <el-table-column property="totolRunTime" label="运行时长" width="100"></el-table-column>
                  <el-table-column property="normalRunTimeStr" label="正常运行时长" width="105"></el-table-column>
                  <el-table-column property="errorRunTimeStr" label="异常运行时长" width="105"></el-table-column>
                  <el-table-column property="componentRatio" label="组件可用比" width="100"></el-table-column>
                  <el-table-column property="updateTime" label="更新时间" width="100"></el-table-column>
                  <el-table-column  label="操作" width="100">
                    <el-button type="text">刷新</el-button>
                  </el-table-column>
                </el-table>
              </el-dialog>
              </template>
            </el-table-column>
          </el-table>
          <div class="block" id="vlok">
          <el-pagination
              @size-change="handleSizeChange2"
              @current-change="handleCurrentChange2"
              :current-page.sync="currentPage"
              :page-size="10"
              layout="total, prev, pager, next"
              :total="total">
        </el-pagination>
      </div>
        </el-tab-pane>
        <!-- 选项卡三能力监控布局 -->
        <el-tab-pane label="能力监控" name="third">
          <!-- 搜索 form表单 -->
          <div class="search">
            <el-form :inline="true"  class="demo-form-inline">
              <!-- <el-form-item label="主机名称" class="right">
                <el-input v-model="formInline.name" placeholder="主机名称"></el-input>
              </el-form-item> -->
              <span class="demonstration">选择能力</span>
              <el-select v-model="formData.name" placeholder="请选择" class="right">
                <el-option
                  v-for="item in arr"
                  :key="item.id"
                  :label="item.name"
                  :value="item.name"
                ></el-option>
              </el-select>
            <span class="demonstration">能力状态</span>
              <el-select v-model="abilityStatus" filterable placeholder="请选择" class="right">
                <el-option
                  v-for="item in option2"
                  :key="item.value"
                  :label="item.name"
                  :value="item.value"
                ></el-option>
              </el-select>
              <el-form-item>
                <el-button type="primary" @click="onSubmit3" class="right">搜索</el-button>
              </el-form-item>
            </el-form>
          </div>
          <!-- table表格布局 -->
          <el-table
            :data="tableData3.slice((currentPage-1)*pagesize,currentPage*pagesize)"
            style="width: 100%;"
            class="tabP"
          >
            <el-table-column prop="id" label="能力ID" width="180"></el-table-column>
            <el-table-column prop="name" label="能力名称" width="180"></el-table-column>
            <el-table-column prop="abilityTotalNum" label="能力服务数量"></el-table-column>
            <el-table-column prop="normalAbilityNum" label="正常状态服务数量"></el-table-column>
            <el-table-column prop="alarmAbilityNum" label="告警组件数量"></el-table-column>
            <el-table-column prop="updateTime" label="更新时间"></el-table-column>

            <el-table-column label="操作">
              <!-- 能力监控操作详情 -->
              <template slot-scope="scope">
                <el-button type="text" @click="getDetail3(scope.$index, scope.row)">
                  <i class="icon iconfont icon-chakan" style="font-size:18px; font-weight:bold;"></i>
                </el-button>
                 <el-button type="text" @click="getRefresh">
                  <i class="el-icon-refresh" style="font-size:24px; font-weight:bold;"></i>
                 </el-button>
              <el-dialog 
                width='54%'
              title="能力监控详情" :visible.sync="dialogTableVisible3">
              <span class="demonstration">组件状态</span>
              <el-select v-model="compStatus" placeholder="请选择" class="right">
                <el-option
                  v-for="item in optionss"
                  :key="item.name"
                  :label="item.name"
                  :value="item.value"
                ></el-option>
              </el-select>
              <el-button type="primary" class="right">搜索</el-button>
                <el-table :data="gridData3">
                  <el-table-column property="hostIp" label="能力IP" width="100"></el-table-column>
                  <el-table-column property="port" label="能力端口" width="100"></el-table-column>
                  <el-table-column property="status" label="能力状态" width="100"></el-table-column>
                  <el-table-column property="totolRunTime" label="运行时长" width="100"></el-table-column>
                  <el-table-column property="normalRunTimeStr" label="正常运行时长" width="105"></el-table-column>
                  <el-table-column property="errorRunTimeStr" label="异常运行时长" width="105"></el-table-column>
                  <el-table-column property="componentRatio" label="组件可用比" width="100"></el-table-column>
                  <el-table-column property="updateTime" label="更新时间" width="100"></el-table-column>
                  <el-table-column  label="操作" width="100">
                    <el-button type="text">刷新</el-button>
                  </el-table-column>
                </el-table>
              </el-dialog>
              </template>
            </el-table-column>
          </el-table>
        </el-tab-pane>
      </el-tabs>
      <div class="block" id="vlok">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page.sync="currentPage"
          :page-size="10"
          layout="total, prev, pager, next"
          :total="total"
        ></el-pagination>
      </div>
      
    </div>
    <!-- 操作性情的弹窗结构 -->
      <el-dialog
        class="headers"
        :title="title"
        :visible.sync="dialogEditgsVisible"
        width="50%"
        center
      >
        <div style="width:100%;height:600px;">
          <div class="information">
            <div>
              <p>服务器信息</p>
              <p>
                <span>主机名称: {{hostDetail.hostName}}</span>
                <span>主机IP: {{hostDetail.hostIp}}</span>
                <span>操作系统: {{hostDetail.operationSystem}}</span>
              </p>
              <p>
                <span>CPU: {{hostDetail.cpuCore}}核</span>
                <span style="margin-left:100px;">内存：{{hostDetail.cpuCore}}</span>
              </p>
            </div>
            <div class="myCPU">
              <div>
                <p>CPU状态监控</p>
                <p style="text-align:center;">CPU指标</p>
                <ul class="list">
                  <li>CPU主频·········{{hostDetail.cpuFrequency}} GHZ</li>
                  <li>CPU个数·········{{hostDetail.cpuNum}} 个</li>
                  <li>CPU内核·········{{hostDetail.cpuCore}} 个</li>
                </ul>
                <div class="el-progress-circle" style="height: 126px; width: 126px;">
                  <!-- <div style='width:100px;height:100px;border-radius:50%;border:solid 1px #f00;'>{{hostDetail.cpuUtility}}%</div> -->
                  <canvas ref="canvas1" id="canvas1" width="100" height="100" background="red">{{hostDetail.cpuUtility}}%</canvas>
                  <p style="width:200px;font-size:14px;text-align:center;">CPU使用率</p>
                </div>
              </div>
              <div>
                CPU使用状态
                <div style='width:600px;height:200px;' ref='cpu'></div>      
              </div>
            </div>
            <div class="myCPU">
              <div>
                <p>内存态监控</p>
                <p style="text-align:center;">内存指标</p>
                <ul class="list">
                  <li>内存容量·········{{hostDetail.ramCapacity}}G</li>
                </ul>
                <div class="el-progress-circle" style="height: 100px; width: 100px; text-align:center;">
                  <!-- <div style='width:100px;height:100px;border-radius:50%;border:solid 1px #f00;'>{{hostDetail.ramUtility}}%</div> -->
                  <!-- <el-progress type="circle" :percentage="hostDetail.ramUtility" width="90" ></el-progress> -->
                  <canvas ref="canvas2" id="canvas2" width="100" height="100">{{hostDetail.ramUtility}}%</canvas>
                  <p style="width:200px;font-size:14px;text-align:center;">内存使用率</p>
                </div>
              </div>
              <div>
                内存使用状态
                <div style='width:600px;height:200px;' ref='ram'></div>      
              </div>
            </div>
          </div>
        </div>
      </el-dialog>
  </div>
</template>

 <script>
import echarts from "echarts";
export default {
  data() {
    return {
      canvas1Value: 0,
      canvas2Value: 0,
      x:0,
      pagination:{
        start:1,
        pageSize: 3,
        total: 0
      },
      arr:[],
      total:0,
      formData:{
        name:'',
        status:''
      },
      gridData: [{
            // componentRatio: "",  
            // createTime: "",
            // errorRunTimeStr: "",
            // hostIp: "",
            // id: 3,
            // msg: "",
            // name: "",
            // normalRunTimeStr: "",
            // port: "",
            // status: 1,
            // totolRunTime: "",
            // updateTime: ""
      }],
        dialogTableVisible: false,

      gridData3: [{
            componentRatio: "100.0%",  //组件可用比
            createTime: "2019-10-15 15:16:10",  
            errorRunTimeStr: "0小时0分55秒",  //异常运行时长
            hostIp: "192.168.1.203",  //组件IP
            id: 1,
            name: "人脸识别",   
            normalRunTimeStr: "14天6小时0分12秒",  //正常运行时长
            port: "80",  //能力端口
            status: 0,   //组件状态
            totolRunTime: "14天6小时0分12秒",  //运行时长
            updateTime: "2019-10-20 21:58:13"  //更新时间
        }],
        dialogTableVisible3: false,

      activeName: "first",
      currentPage: 1, //初始页
      pagesize: 10, //每页的数据
      //进度条
      percentage: 20,
      percentage1: 35,
      compStatus:'',
      compName:"",
      selectAbility:"",  //选择能力
      abilityStatus:"",  //能力状态
      customColor: "#409eff",
      customColors: [
        { color: "#f56c6c", percentage: 65 },
        { color: "#e6a23c", percentage: 70 },
        { color: "#5cb87a", percentage: 75 },
        { color: "#1989fa", percentage: 80 },
        { color: "#6f7ad3", percentage: 100 }
      ],
      pickerOptions: {
        shortcuts: [
          {
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
              picker.$emit("pick", [start, end]);
            }
          },
          {
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 30);
              picker.$emit("pick", [start, end]);
            }
          },
          {
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 90);
              picker.$emit("pick", [start, end]);
            }
          }
        ]
      },
      value1: "",
      value2: "",
      formInline: {
        user: "",
        hostName: "",
        region: "",
        hostIp: ""
      },
      formInline2:{
        name:"",
        id:"",

      },

      addForm: {
        name: "",
        sort: 99
      },
      editForm: {
        name: "",
        sort: 99
      },
      title: "",
      title1: "",
      dialogAddgsVisible: false,
      dialogEditgsVisible: false,
      dialogEditgsVisible1: false,
      options: [],
      value: "",
      optionss: [
        {
          name:"正常",
          value:0
        },
        {
          name:"告警",
          value:1
        },
      ],
        option2: [
        {
          name:"正常3",
          value:0
        },
        {
          name:"告警3",
          value:1
        },
      ],

      values: "",
      hostDetail: {},
      rules: {
        name: [
          { required: true, message: "请输入名称" },
          { min: 2, max: 10, message: "SSSSSSS", trigger: "blur" }
        ],
        sort: [{ type: "number", message: "11233552", trigger: "blur" }]
      },
      tableData: [
        // {
        //   id: 1, //id
        //   status: 0, //状态
        //   hostName: "主机", //主机名称
        //   hostIp: "192.168.1.6", //主机IP
        //   operationSystem: "centOS", //操作系统
        //   cpuFrequency: "2.5", //cpu利用率
        //   //内存利用率
        //   updateTime: "2019-10-20 21:35:29" //更新时间
        // }
      ],
      tableData2: [
        // {
        //   alarmTotalComponents: 6, //告警组件数量
        //   id: 1, //组件ID
        //   name: "tomcat", //组件名称
        //   normalTotalComponents: 0, //正常组件数量
        //   totalComponents: 6, //组件数量
        //   updateTime: "2019-10-20 21:41:43" //更新时间
        // }
      ],
      tableData3: [
        // {
        //   abilityTotalNum: 1, //能力服务数量
        //   alarmAbilityNum: 0, //告警状态服务数量
        //   id: 5, //服务ID
        //   name: "人脸识4", //服务名称
        //   normalAbilityNum: 1, //正常状态服务数量
        //   updateTime: "2019-10-20 21:58:20" //更新时间
        // }
      ],
      cpuOptions: {
        xAxis: [],
        series: [],
      },
      ramOptions: {
        xAxis: [],
        series: [],
      }
    };
  },
  methods: {
    //画圆环
    getCircal(ele,value) {
    // console.log(ele,value,'12345678-*********')
    let x = value; // 动态变动的进度值
    let ctx = ele.getContext('2d');
    // 最外层圆环
    ctx.beginPath();
    ctx.lineWidth = 5; 
    ctx.strokeStyle = '#F0F8FF';
    ctx.arc(50, 50, 40, 0, 2 * Math.PI);
    ctx.restore();
    ctx.stroke();
    // 内层浅蓝色圆环
    ctx.beginPath();
    ctx.lineWidth = 5; 
    ctx.strokeStyle = '#87CEFA';
    ctx.arc(50, 50, 40, 0, 2 * Math.PI);
    ctx.restore();
    ctx.stroke();
    // 深色进度条圆环
    ctx.beginPath();
    ctx.lineWidth = 10;
    ctx.strokeStyle = '#1E90FF';
    ctx.arc(50, 50, 50, -90 * Math.PI / 180, (x * 3.6 - 90) * Math.PI / 180);
    ctx.stroke();
    // 中心进度文字
    ctx.font = '20px Arial';
    ctx.fillStyle = '#f30';
    ctx.textBaseline = 'middle';
    ctx.textAlign = 'center';
    ctx.fillText(`${x.toFixed(2)}%`, 50, 50);
    },
    //选项卡三的刷新
    getRefresh() {
      console.log('1234556')
      this.$axios.post('/oms-basic/webappInfo!list.json').then(res =>{
        this.tableData3  = [];
        setTimeout(() => {
        this.tableData3 = res.data.list;
        }, 300);
        console.log(this.tableData3,'123456')
      }).catch(error =>{
        console.log(error)
      })
    },

    //选项二的详情
    getDetail2(index, row) {
      let  mydata = {
        name:row.name
      }
      console.log(mydata,'mydata')
      var param = this.$qs.stringify(mydata)
      this.dialogTableVisible = true;
      this.$axios.post('/oms-basic/softWareInfo!getSoftWareInfoListByName.json',param
      ).then(res => {
        console.log(res,'res')
             this.gridData = res.data.list;
             console.log(this.gridData)
      }).catch(error => {

      })
    },
    //选项卡三的详情
    getDetail3(index,row) {
      this.dialogTableVisible3 = true;
      let mydata3 = {
        name:row.name
      }
      var param3 = this.$qs.stringify(mydata3);
      this.$axios.post('/oms-basic/webappInfo!get.json',param3).then(res => {
        this.gridData3 = res.data.list;
      }).catch(error => {
        console.log(error)
      })
    },
    //选项卡三的搜索
    //obj 就是输入框带的值,发请求带的参数。
    getSearch3(obj) {
      this.$axios.post('/oms-basic/webappInfo!list.json',this.$qs.stringify(obj)).then(
        res => {
          this.tableData3 = res.data.list;
          console.log(res,'res')
        }).catch(error => {
          console.log(error)
        })
    },
    //调取获取主机信息列表(主机监控)table表格接口
    getMessage() {
      this.$axios
        .post("/oms-basic/hardWareInfo!list.json", {})
        .then(res => {
          this.tableData = [].concat(res.data.list);
          this.total = res.data.count;
        })
        .catch(error => {
          console.log(error);
        });
    },
    //选项卡二的搜索功能
     onSubmit2() {
       let formData={};
      if (this.compName) {
        formData.name = this.compName;
      }
      if (this.compStatus!=undefined||this.compStatus!='') {
        formData.status = this.compStatus;
      }
      this.$axios
        .post(
          "/oms-basic/softWareInfo!list.json",
          this.$qs.stringify(formData)
        )
        .then(res => {
          this.tableData2 = res.data.list;
          console.log(res, "search");
        })
        .catch(err => {});
        console.log(err)
    },
    //选项卡三的搜索功能
    onSubmit3() {
      let {name,status} = this.formData;  //解构赋值 formData里面有两个键
      if(!(name || status ))return;
      let formData = {};
      name && (formData.name = name);  //name 的值赋值给formData.name
      status && (formData.status = status);  //status的值值赋值给formData.status
      // this.pagination = {
      //   start:1,  //做分页的时候:传当前页与一页有几条数据
      //   pageSize: 3,
      //   total: 0
      // };
      // Object.keys(this.pagination).forEach(item=>formData[item]=this.pagination[item]);
      console.log("formdata",formData)
      this.getSearch3(formData);
    },
    //调取选项卡三的下拉选择能力接口
    select() {
      this.$axios.post('/oms-basic/webappInfo!getWebappInfoList.json').then(res =>{
        // console.log(res.data.list,'123456')
        this.arr = res.data.list;
        // console.log(this.arr,'99999999999')        
      }).catch(error => {
        console.log(error)
      })
    },
    //调取选项卡一(主机监控)搜索接口
    onSubmit() {
      if (!(this.formInline.hostName || this.formInline.hostIp)) {
        return;
      }
      let formData = {};
      if (this.formInline.hostName) {
        formData.hostName = this.formInline.hostName;
      }
      if (this.formInline.hostIp) {
        formData.hostIp = this.formInline.hostIp;
      }
      this.$axios
        .post("/oms-basic/hardWareInfo!list.json", this.$qs.stringify(formData))
        .then(res => {
          this.tableData = res.data.list;
        })
        .catch(error => {
          console.log(error);
        });
    },
    //
      getcomponentName() {
        this.$axios.post('/oms-basic/softWareInfo!list.json').then(res => {
          this.options = res.data.list;
          console.log(this.options,'this.options')
        }).catch(error => {
          console.log(error)
        })
      },
    //获取组件信息列表(组件监控)table表格接口
    getComponent() {
      this.$axios
        .post("/oms-basic/softWareInfo!list.json", {})
        .then(res => {
          // console.log(res);
          this.tableData2 = [].concat(res.data.list);
        })
        .catch(error => {
          console.log(error);
        });
    },
    //调取能力监控table表格接口
    getAbility() {
      this.$axios
        .post("/oms-basic/webappInfo!list.json", {})
        .then(res => {
          // console.log(res);
          this.tableData3 = [].concat(res.data.list);
        })
        .catch(error => {
          console.log(error);
        });
    },
    //进度条
    customColorMethod(percentage) {
      if (percentage < 30) {
        return "#909399";
      } else if (percentage < 70) {
        return "#e6a23c";
      } else {
        return "#67c23a";
      }
    },
    increase() {
      this.percentage += 10;
      if (this.percentage > 100) {
        this.percentage = 100;
      }
    },
    decrease() {
      this.percentage -= 10;
      if (this.percentage < 0) {
        this.percentage = 0;
      }
    },
    //
    handleClick(tab, event) {
      console.log(tab, event);
    },
    currentChangePage(list) {
      let from = (this.currentPage - 1) * this.pageSize;
      let to = this.currentPage * this.pageSize;

      for (; from < to; from++) {
        if (list[from]) {
          this.tableData.push(list[from]);
        }
      }
    },
    // 初始页currentPage、初始每页数据数pagesize和数据data
    handleSizeChange: function(size) {
      this.pagesize = size;
      // console.log(this.pagesize); //每页下拉显示数据
    },
    handleCurrentChange: function(currentPage) {
      this.currentPage = currentPage;
      // console.log(this.currentPage)  //点击第几页
    },
    // 初始页currentPage、初始每页数据数pagesize和数据data
    handleSizeChange2: function(size) {
      this.pagesize = size;
      // console.log(this.pagesize); //每页下拉显示数据
    },
    handleCurrentChange2: function(currentPage) {
      this.currentPage = currentPage;
      // console.log(this.currentPage)  //点击第几页
    },


    deleteRow1(index) {
      this.tableData.forEach((item, index) => {});

      this.dialogEditgsVisible1 = false;
    },

    saveEditForm(aaa) {
      this.$refs[aaa].validate(valid => {
        // console.log(this.$refs[aaa]);
        if (valid) {
          // this.$axios.put(`http://localhost:3000/admin/categories/${this.editForm.id}`,this.editForm).then( res =>{
          //   alert('更新成功');
          this.dialogEditgsVisible = false;
          this.init();
          // console.log(valid);
          // })
        }
      });
    },
    open() {
      this.$alert(
        '<div style="width:500px; height:500px; background:red;"></div>',
        "主机监控详情",
        {
          dangerouslyUseHTMLString: true
        }
      );
    },
    // 请求主机详情
    queryHotDetail(obj){
      this.$axios.post('/oms-basic/hardWareInfo!get.json',this.$qs.stringify(obj)).then(res=>{
        if(res.data.code === '10000'){
          this.hostDetail = res.data.hardwareInfo[0];
          console.log("数据",res.data.hardwareInfo)
          let xAxis = [],cpuY = [],ramY = [];
          res.data.hardwareInfo.forEach((item,index)=>{
            xAxis.push(item.createTime.split(' ')[1].substring(0,5));
            cpuY.push(item.cpuUtility);
            ramY.push(item.ramUtility);
          })
          console.log(xAxis,cpuY,ramY)
          this.cpuOptions = {
            xAxis,
            series: cpuY
          };
          this.ramOptions = {
            xAxis,
            series: ramY
          };
          this.title = "主机监控详情";
          this.dialogEditgsVisible = true;
          this.$nextTick(()=>{
            this.generatorChart(this.$refs.cpu,this.cpuOptions); // CPU曲线图
            this.generatorChart(this.$refs.ram,this.ramOptions); // 内存曲线图
          })
        }
      })
    },
    generatorChart(ele,{xAxis,series}){
      let app = echarts.init(ele);
      let option = {
        tooltip : {
          trigger: 'axis',
          axisPointer : {            // 坐标轴指示器，坐标轴触发有效
            type : 'shadow'        // 默认为直线，可选为：'line' | 'shadow'
          },
        },
        xAxis: {
        type: 'category',
        data: xAxis
        // data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
        },
        yAxis: {
          type: 'value'
        },
        series: [{
            data: series,
            name: '利用率',
            // data: [10,2,1,5,6],
            type: 'line',
            smooth: true,
            color: ['#3398DB'],
            areaStyle: {
                normal: {
                    color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [{
                        offset: 0,
                        // color: '#8ec6ad'
                        color: '#3398DB'
                    }, {
                        offset: 1,
                        color: '#ffe'
                    }])
                }
            },
        }]
      };
      app.setOption(option);
    },
    //点击详情
    editgsForm(hostIp) {
      this.queryHotDetail({hostIp});
      this.getCircal(this.$refs.canvas1,this.canvas1Value);  //画圆环
      this.getCircal(this.$refs.canvas2,this.canvas2Value);  //画圆环
      console.log(this.$refs.canvas1,'this.$refs.canvas1,')
    }
  },
  mounted() {
      // this.$nextTick(()=>{
      // console.log(this.$refs.canvas1,'this.$refs.canvas1')
      // })
    this.getMessage(); //调取table表格数据接口
    // this.getSearch();  //调取搜索接口
    this.getComponent(); //获取组件信息列表table表格(组件监控)接口
    this.getAbility(); //调取能力监控table表格接口
    this.getcomponentName();
    this.select();  //调取下拉框接口
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
.mHome {
  height: 870px;
  background: #fff;
  overflow: hidden;
  position: relative;
  /deep/ .el-tabs__nav.is-top {
    width: 100%;
    height: 50px;
    display: flex;
    flex-direction: rows;
    /deep/ .el-tabs__item.is-top {
      flex: 1;
      text-align: center;
    }
  }
}
.home {
  height: 870px;
  background: #fff;
  overflow: hidden;
}

.search {
  padding: 12px 0 0 12px;
}
#vlok {
  position: absolute;
  left: 30%;
  bottom: 5px;
}
.right {
  margin-right: 24px;
}
#Detailedy {
  width: 100%;
  height: 50px;
  border-bottom: 1px solid #ccc;
  border-top: 1px solid #ccc;
  line-height: 50px;
  padding-left: 10px;

  box-sizing: border-box;
}
#bd {
  color: #3584f3;
  background: #d5e6ff;
  font-weight: bold;
  border-radius: 50%;
}
.active {
  background: #3584f3;
  color: #fff;
}
// .block {
//   position: fixed;
//   left: 37%;
//   top: 915px;
// }
.icon_tu {
  display: inline-block;
  position: fixed;
  right: 4%;
}
.information {
  div:nth-child(1) {
    width: 100%;
    height: 100px;
    border-bottom: 1px solid #ccc;
    padding: 0 20px 20px 20px;
    box-sizing: border-box;
    background: #fff;
    p {
      line-height: 30px;
      span {
        margin-left: 22px;
      }
    }
  }
  div:nth-child(2) {
    width: 100%;
    height: 240px;
    border-bottom: 1px solid #ccc;
    padding: 0 20px 20px 20px;
    box-sizing: border-box;
    background: #fff;
  }
  div:nth-child(3) {
    width: 100%;
    height: 240px;
    border-bottom: 1px solid #ccc;
    padding: 0 20px 20px 20px;
    box-sizing: border-box;
    background: #fff;
  }
}
.myCPU {
  display: flex;
  flex-direction: row;
  div:nth-child(1) {
    width: 240px;
    height: 100%;
    border: none;
    .list {
      width: 100%;
      padding-left: 60px;
      box-sizing: border-box;
      li {
        line-height: 15px;
        font-size: 10px;
      }
    }
  }
  div:nth-child(2) {
    flex: 1;
    background: #fff;
  }
}
/deep/ .svgTick {
  margin-left: 40px;
  margin-top: -16px;
}
/deep/ .el-dialog__header {
  background: #3584f3;
  color: #fff;
  /deep/ .el-dialog__title {
    color: #fff;
  }
  /deep/ .el-dialog__close.el-icon.el-icon-close::before {
    color: #fff;
  }
}
</style>
