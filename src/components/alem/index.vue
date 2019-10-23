<template>
  <div class="mHome">
    <div class="titleQ">
      <el-tabs v-model="activeName" @tab-click="handleClick">
        <el-tab-pane label="告警列表" name="first">
          <div class="search">
            <el-form :inline="true" :model="formData" class="demo-form-inline">
              <!-- 告警源 -->
              <!-- <span class="demonstration">告警源</span> -->
              <el-form-item label="告警策略" class="right">
                <el-select></el-select>
              </el-form-item>
              <!-- 告警类型 -->
              <!-- <span class="demonstration">告警类型</span> -->
              <el-form-item label="告警策略" class="right">
                <el-select></el-select>
              </el-form-item>
              <!-- 告警策略 -->
              <el-form-item label="告警策略" class="right">
                <el-select></el-select>
              </el-form-item>
              <!-- 监控项 -->
              <el-form-item label="告警策略" class="right">
                <el-input placeholder="来源IP" style="width:170px;" class="tetMy"></el-input>
              </el-form-item>
              <!-- 时间选择 -->
              <!-- <span class="demonstration">时间选择</span> -->
              <el-date-picker
                type="daterange"
                align="right"
                class="right MyData"
                unlink-panels
                range-separator="至"
                start-placeholder="开始日期"
                end-placeholder="结束日期"
                :picker-options="pickerOptions"
              ></el-date-picker>
              <!-- 搜索 导出当前按钮 -->
              <el-form-item>
                <el-button type="primary" @click="onSubmit" class="right">搜索</el-button>
                <el-button type="primary" class="right" @click="exportCurrent">发 送</el-button>
              </el-form-item>
            </el-form>
          </div>
          <el-table
            :data="tableData.slice((currentPage-1)*pagesize,currentPage*pagesize)"
            style="width: 100%;"
            class="tabP"
          >
            <el-table-column type="selection" width="65"></el-table-column>
            <el-table-column prop="id" label="告警ID" width="180"></el-table-column>
            <el-table-column prop="source" label="告警项" width="180"></el-table-column>
            <el-table-column prop="monitorItem" label="监控项"></el-table-column>
            <el-table-column prop="createTime" label="告警时间"></el-table-column>
            <el-table-column prop="strategy" label="告警策略"></el-table-column>
            <el-table-column prop="msg" label="告警信息"></el-table-column>

            <el-table-column label="操作">
              <template slot-scope="scope">
                <el-radio-group v-model="radio">
                  <el-radio :label="scope.$index">已处理</el-radio>
                  <el-radio :label="scope.$index+'12'">忽略</el-radio>
                </el-radio-group>
              </template>
            </el-table-column>
          </el-table>
          <div class="block">
            <el-pagination
              @size-change="handleSizeChange"
              @current-change="handleCurrentChange"
              :current-page.sync="currentPage1"
              :page-size="100"
              layout="total, prev, pager, next"
              :total="total"
            ></el-pagination>
          </div>
        </el-tab-pane>
        <el-tab-pane label="告警配置管理" name="second">
          <div class="search">
            <el-form :inline="true" :model="formInline" class="demo-form-inline">
              <el-form-item label="联系人姓名" class="right plain_ome">
                <el-input v-model="tableDataValue" placeholder="联系人姓名"></el-input>
              </el-form-item>
              <el-form-item>
                <el-button type="primary" @click="doFilter" class="right">搜索</el-button>
                <el-button type="primary" plain @click="dialogVisible = true" class="right">创建联系人</el-button>
                <!-- 0 -->
                <el-dialog title="提示" :visible.sync="dialogVisible" width="30%">
                  <el-row>
                    租户名称
                    <el-input v-model="productName" style="width: 70%;margin: 10px 0 10px 0"></el-input>
                  </el-row>
                  <el-row>
                    授权能力
                    <el-input v-model="value" style="width: 70%;margin: 10px 0 10px 0"></el-input>
                  </el-row>
                  <div slot="footer" class="dialog-footer">
                    <el-button @click="dialogVisible = false">取 消</el-button>
                    <el-button type="primary" @click="addData">确 定</el-button>
                  </div>
                </el-dialog>
                <!-- 0 -->
              </el-form-item>
            </el-form>
          </div>
          <el-table
            :data="tableDatas.slice((currentPage-1)*pagesize,currentPage*pagesize)"
            style="width: 100%;"
            class="tabP"
          >
            <el-table-column prop="id" label="联系人ID" width="180"></el-table-column>
            <el-table-column prop="name" label="联系人姓名" width="180"></el-table-column>
            <el-table-column prop="phone" label="联系人电话"></el-table-column>
            <el-table-column prop="email" label="联系人邮箱"></el-table-column>
            <el-table-column prop="msg" label="告警策略"></el-table-column>
            <el-table-column prop="createTime" label="创建时间"></el-table-column>
            <el-table-column prop="createTime" label="更新时间"></el-table-column>
            <el-table-column label="操作">
              <template slot-scope="scope">
                <!-- 编辑 -->
                <el-button type="text" @click="editgsForm(scope.$index, scope.row)">
                  <i class="icon iconfont icon-bianji" style="font-size:18px; font-weight:bold;"></i>
                </el-button>
                <!-- 编辑弹窗 dialog-->
                <el-dialog
                  class="headers"
                  :title="title"
                  :visible.sync="dialogEditgsVisible"
                  width="30%"
                  center
                  @close="closeDialogVisible"
                >
                  <el-form :model="editForm" :rules="rules" ref="editForm">
                    <el-form-item label="联系人ID" class="add" :label-width="formLabelWidth">
                      <el-input readonly v-model="editForm.id" autocomplete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="联系人姓名" class="add" :label-width="formLabelWidth">
                      <el-input v-model="editForm.name" autocomplete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="联系人电话" class="add" :label-width="formLabelWidth">
                      <el-input v-model="editForm.phone" autocomplete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="联系人邮箱" class="add" :label-width="formLabelWidth">
                      <el-input v-model="editForm.email" autocomplete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="微信昵称" class="add" :label-width="formLabelWidth">
                      <el-input v-model="editForm.wechatName" autocomplete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="告警策略" class="add" :label-width="formLabelWidth">
                      <el-input v-model="editForm.createTime" autocomplete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="告警策略" class="add" :label-width="formLabelWidth">
                      <el-input v-model="editForm.msg" autocomplete="off"></el-input>
                    </el-form-item>
                  </el-form>

                  <div slot="footer" class="dialog-footer">
                    <el-button @click="dialogEditgsVisible = false">取 消</el-button>
                    <el-button type="primary" @click="saveEditForm()">确 定</el-button>
                  </div>
                </el-dialog>
                <!-- 删除 -->
                <el-button type="text" @click="open(scope.row.id)">
                  <i
                    class="icon iconfont icon-shanchu"
                    style="font-size:18px; color:orange; font-weight:bold;"
                  ></i>
                </el-button>
              </template>
            </el-table-column>
          </el-table>
          <div class="block">
            <el-pagination
              @size-change="handleSizeChange"
              @current-change="handleCurrentChange"
              :current-page.sync="currentPage1"
              :page-size="100"
              layout="total, prev, pager, next"
              :total="1000"
            ></el-pagination>
          </div>
        </el-tab-pane>
      </el-tabs>
    </div>
  </div>
</template>

 <script>
import echarts from "echarts";
export default {
  data() {
    return {
      radio: "",
      total: 0,
      activeName: "first",
      currentPage: 1, //初始页
      pagesize: 10, //每页的数据
      //进度条
      percentage: 20,
      percentage1: 35,
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
      form: {
        name: "",
        abilityId: [],
        date1: "",
        date2: "",
        delivery: false,
        type: [],
        resource: "",
        desc: ""
      },
      formInline: {
        user: "",
        name: "",
        region: ""
      },
      addForm: {
        name: "",
        sort: 99
      },
      editForm: {
        name: "",
        sort: 99,
        arrayAbility: [],
        temArr: []
      },
      title: "",
      title1: "",
      dialogAddgsVisible: false,
      dialogEditgsVisible: false,
      dialogEditgsVisible1: false,
      dialogVisible: false,
      tableDataValue: "",
      options: [
        {
          value: "选项1",
          label: "黄金糕"
        },
        {
          value: "选项2",
          label: "双皮奶"
        },
        {
          value: "选项3",
          label: "蚵仔煎"
        },
        {
          value: "选项4",
          label: "龙须面"
        },
        {
          value: "选项5",
          label: "北京烤鸭"
        }
      ],
      value: "",
      optionss: [
        {
          values: "选项1",
          labels: "黄金糕"
        },
        {
          values: "选项2",
          labels: "双皮奶"
        },
        {
          values: "选项3",
          labels: "蚵仔煎"
        },
        {
          values: "选项4",
          labels: "龙须面"
        },
        {
          values: "选项5",
          labels: "北京烤鸭"
        }
      ],
      values: "",

      rules: {
        name: [
          { required: true, message: "请输入名称" },
          { min: 2, max: 10, message: "SSSSSSS", trigger: "blur" }
        ],
        sort: [{ type: "number", message: "11233552", trigger: "blur" }]
      },
      tableData: [],
      tableDatas: []
    };
  },
  methods: {
    //数据展示(告警列表)
    getdatas() {
      this.$axios.post("/oms-basic/warnInfoRelate!list.json").then(res => {
        console.log(res, "告警列表");
        this.tableData = res.data.list;
        this.total = res.data.count;
      });
    },
    //数据展示(联系人列表)
    getData() {
      this.$axios.post("/oms-basic/emergencyContact!list.json").then(res => {
        console.log(res.data.list, "联系人列表");
        this.tableDatas = res.data.list;
      });
    },
    //搜索
    doFilter() {
      // if (this.tableDataName == "" && this.tableDataValue == "") {
      //   this.$message.warning("查询条件不能为空！");
      //   return;
      // }
      //this_.tableData = []; //tableData列表数据 //每次手动将数据置空,因为会出现多次点击搜索情况
      this.filtertableData = []; //过滤后的数据
      var IDcode = {
        name: this.tableDataValue
      };
      this.$axios
        .post(
          "/oms-basic/emergencyContact!list.json",
          this.$qs.stringify(IDcode)
        )
        .then(res => {
          console.log(res, "搜索结果");
          console.log(res.data.list);
          var subjectY = res.data.list;
          // if(subjectY.tenantID === this.tableDataName.value &&
          //   subjectY.code == this.tableDataValue.value
          // ){
          //   this.filtertableData.push(subjectY);
          // }
          this.tableDatas = subjectY;
          this.total = res.data.count;
          console.log(this.tableDatas, "ssss");
        })
        .catch(error => {
          console.log(error);
        });
      //页面数据改变重新统计数据数量和当前页
      this.currentPage = 1;
      this.totalItems = this.filtertableData.length;
      //渲染表格,根据值
      this.currentChangePage(this.filtertableData);
      //页面初始化数据需要判断是否检索过
      this.flag = true;
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
      console.log(this.pagesize); //每页下拉显示数据
    },
    handleCurrentChange: function(currentPage) {
      this.currentPage = currentPage;
      // console.log(this.currentPage)  //点击第几页
    },
    deleteRow1(index) {
      this.tableData.forEach((item, index) => {});

      this.dialogEditgsVisible1 = false;
    },

    /**
     *
     * @param
     */
    editgsForm(index, row) {
      this.dialogEditgsVisible = true;
      console.log(this.editForm.temArr);
      row.temArr = this.editForm.temArr;
      console.log(row, "row");

      this.title = "编辑";
      this.editForm = row;
      var arr1 = [],
        arr2 = [];
      console.log(row.abilityIDs, "row.abilityIDs"); //111,116,115,114,122,113,112,119 row.abilityIDs
      //arr1 = row.abilityIDs.split(","); //arr1是什么, row.abilityIDs是什么???
      console.log(arr1, "arr1"); //["111", "116", "115", "114", "122", "113", "112", "119"]
      arr2 = row.abilityNames.split(",");
      console.log(arr2, "arr2");
      var arr = [];
      this.form.abilityId = row.abilityIDs; //arr赋值给editForm.arrayAbility
      this.editForm.arrayAbility = this.editForm.temArr;
      console.log(this.editForm.temArr);
    },
    saveEditForm() {
      var that = this;
      var param = new URLSearchParams();
      param.append("id", this.editForm.id);
      param.append("name", this.editForm.name);
      param.append("email", this.editForm.email);
      param.append("phone", this.editForm.phone);
      param.append("status", this.editForm.status);
      param.append("wechatName", this.editForm.wechatName);
      param.append("msg", this.editForm.msg);
      console.log(this.form.abilityId);
      var data = {
        id: this.editForm.id,
        name: this.editForm.name,
        email: this.editForm.email,
        phone: this.editForm.phone,
        status: this.editForm.status,
        wechatName: this.editForm.wechatName,
        msg: this.editForm.msg
      };
      // var header={
      //   headers:{
      //     "Content-Type":"application/x-www-form-urlencoded"
      //   }
      // }
      this.$axios
        .post("/oms-basic/emergencyContact!save.json", data)
        .then(function(response) {
          console.log(response, "111111");
          if (response.data.code == 10000) {
            that.dialogEditgsVisible = false;
          }
        })
        .catch(function(error) {
          console.log(error);
        });

      this.$axios.post("/oms-basic/emergencyContact!list.json").then(res => {
        console.log(res.data.list, "联系人列表");
        this.tableDatas = res.data.list;
      });
    },
    //点击删除是触发函数
    open(index) {
      this.$confirm("此操作将删除该数据, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
        center: true
      })
        .then(() => {
          var that = this;
          console.log(index, "下标");
          var par = {
            id: index
          };
          console.log(par);
          that.$axios
            .post(
              "/oms-basic/emergencyContact!delete.json",
              that.$qs.stringify(par)
            )
            .then(res => {
              console.log(res);
              this.$axios
                .post("/oms-basic/emergencyContact!list.json")
                .then(res => {
                  console.log(res.data.list, "联系人列表");
                  this.tableDatas = res.data.list;
                });
            });
        })
        .catch(() => {
          console.log("11111111");
          this.$message({
            type: "info",
            message: "已取消删除"
          });
        });
    },

    //////
    addData() {
      //显示弹窗
      var that = this;
      that.dialogVisible = false;
      //调取添加接口
      // let header = {
      //   headers: {
      //     "Content-Type": "application/x-www-form-urlencoded"
      //   }
      // };
      //1.
      var params = new URLSearchParams();
      params.append("tenantName", that.productName);
      params.append("abilityIDs", that.value);
      //2. that.$qs.stringify(params1)

      var params1 = {
        name: that.productName,
        email: that.value
      };
      that.$axios
        .post(
          "/oms-basic/emergencyContact!save.json",
          that.$qs.stringify(params1)
        )
        //成功
        .then(res => {
          //返回的数据
          console.log("res777", res);
          //自己定义的空数组tableData
          // this.tableData.splice(0, this.tableData.length)
          var arrs = { tenantName: that.productName, abilityIDs: that.value };
          that.tableData.splice(that.tableData.length, 0, arrs);
          //发送ajax请求获取数据

          this.$axios
            .post("/oms-basic/emergencyContact!list.json")
            .then(res => {
              console.log(res.data.list, "联系人列表");
              this.tableDatas = res.data.list;
            });
        })
        //失败
        .catch(error => {
          console.log(error);
        });
    }
  },
  mounted() {
    this.getData();
    this.getdatas();
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
  position: relative;
}

.search {
  padding: 12px 0 0 12px;
}
.tetMy /deep/ .el-input__inner {
  width: 170px;
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
.block {
  position: fixed;
  left: 37%;
  top: 915px;
}
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
/deep/.el-input__inner {
  width: 120px;
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
.MyData {
  width: 350px;
  /deep/ .el-range-input {
    width: 150px;
  }
}
.plain_ome {
  width: 300px;
  /deep/.el-input__inner {
    width: 200px;
  }
  /deep/ .el-range-input {
    width: 200px;
  }
}
.add {
  width: 500px;

  /deep/.el-input__inner {
    width: 400px;
  }
  /deep/ .el-range-input {
    width: 400px;
  }
}
</style>
