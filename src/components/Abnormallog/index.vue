<template>
  <div class="home">
    <div class="titleQ">异常日志列表</div>
    <div class="search">
      <el-form :inline="true" :model="formInline" class="demo-form-inline">
        <el-form-item label="租户名称" class="right">
          <el-input v-model="formInline.same" placeholder="租户名称"></el-input>
        </el-form-item>

        <span class="demonstration">日志等级</span>
        <el-select v-model="value" filterable placeholder="请选择" class="right">
          <el-option
            v-for="item in options"
            :key="item.label"
            :label="item.label"
            :value="item.label"
          ></el-option>
        </el-select>

        <el-form-item label="来源IP" class="right">
          <el-input v-model="formInline.name" placeholder="来源IP"></el-input>
        </el-form-item>
        <span class="demonstration">时间选择</span>
        <el-date-picker
          v-model="value2"
          type="daterange"
          align="right"
          class="right"
          unlink-panels
          range-separator="至"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          :picker-options="pickerOptions"
        ></el-date-picker>
        <el-form-item>
          <el-button type="primary" @click="onSubmit" class="right">搜索</el-button>
          <el-button type="primary" @click="dao" class="right">导出当前</el-button>
        </el-form-item>
      </el-form>
    </div>
    <el-table
      :data="tableData.slice((currentPage-1)*pagesize,currentPage*pagesize)"
      style="width: 100%;"
      class="tabP"
    >
      <template v-for="(item, index) in tableLabel">
        <el-table-column :key="index" :prop="item.prop" :label="item.label" width></el-table-column>
      </template>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button type="text" @click="viewdetail(scope.$index, scope.row)">
            <i class="icon iconfont icon-chakan" style="font-size:18px; font-weight:bold;"></i>
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <div class="block">
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :page-size="10"
        layout="total, prev, pager, next"
        :total="total"
      ></el-pagination>
    </div>
    <el-dialog title="详情" :visible.sync="dialogDetailsVisible" width="50%" style="text-align:left">
      <ul class="detailBox">
        <li>
          <div class="bg_cyan">日志 ID</div>
          <div class="msgBox">{{detailForm.logID}}</div>
        </li>
        <li>
          <div class="bg_cyan">租户名称</div>
          <div class="msgBox">{{detailForm.tenantName}}</div>
        </li>
        <li>
          <div class="bg_cyan">日志等级</div>
          <div class="msgBox">{{detailForm.level}}</div>
        </li>
        <li>
          <div class="bg_cyan">来源IP</div>
          <div class="msgBox">{{detailForm.source}}</div>
        </li>
        <li>
          <div class="bg_cyan">调用时长</div>
          <div class="msgBox">{{detailForm.updateTime}}</div>
        </li>
        <li style="border-bottom: 1px solid #000;">
          <div class="bg_cyan" style=" height:80px; line-height:80px;">日志信息</div>
          <div class="msgBox" style=" height:80px; line-height:80px;">{{detailForm.msg}}</div>
        </li>
      </ul>
    </el-dialog>
  </div>
</template>

 <script>
export default {
  data() {
    return {
      currentPage: 1, //初始页
      pagesize: 10, //每页的数据
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
        name: "",
        same:"",
        region: ""
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
      total: 0,
      dialogAddgsVisible: false,
      dialogEditgsVisible: false,
      dialogEditgsVisible1: false,
      dialogDetailsVisible: false,
      detailForm: [],
      options: [
        {
          value: "选项1",  //value可以不写
          label: "INFO" //值 
        },
        {
          value: "选项2",
          label: "WARN"
        }
      ],
      optionss: [
        {
          value: "选项1",
          label: "移动"
        },
        {
          value: "选项2",
          label: "电信"
        },
        {
          value: "选项3",
          label: "联通"
        }
      ],
      value: "",
      values: "",

      rules: {
        name: [
          { required: true, message: "请输入名称" },
          { min: 2, max: 10, message: "SSSSSSS", trigger: "blur" }
        ],
        sort: [{ type: "number", message: "11233552", trigger: "blur" }]
      },
      tableData: [],
      tableLabel: [
        { label: "日志ID", prop: "logID" },
        { label: "租户名", prop: "tenantName" },
        { label: "日志等级", prop: "level" },
        { label: "来源IP", prop: "source" },
        { label: "调用时间", prop: "responseTime" },
        { label: "日志类型", prop: "logType" },
        { label: "日志内容", prop: "msg" }
      ]
    };
  },
  methods: {
    dateTransfer(date) {
      var y = date.getFullYear();
      var m = date.getMonth() + 1;
      m = m < 10 ? "0" + m : m;
      var d = date.getDate();
      d = d < 10 ? "0" + d : d;
      var h = date.getHours();
      var minute = date.getMinutes();
      minute = minute < 10 ? "0" + minute : minute;
      return y + "-" + m + "-" + d + " " + "00:00:00";
    },
    onSubmit() {
      if (
        !(
          this.formInline.level ||
          this.formInline.name ||
          this.formInline.same ||
          this.value2 ||
          this.value ||
          this.values
        )
      ) {
        return;
      }
      var formData = {};
      if (this.formInline.level) {
        formData.level = this.formInline.level;
      }
      if (this.formInline.name) {
        formData.source = this.formInline.name;
      }
      if (this.formInline.same) {
        formData.tenantName = this.formInline.same;
      }
      console.log(this.value2, "this.value2");

      if (this.value2 != "" && this.value2 != undefined) {
        formData.startTime = this.dateTransfer(this.value2[0]);
        formData.endTime = this.dateTransfer(this.value2[1]);
      }
      if (this.value) {
        formData.level = this.value;
      }
      console.log(formData, "传递的值");
      this.$axios
        .post(
          "/oms-basic/abilityLog!selectLog.json",
          this.$qs.stringify(formData)
        )
        .then(res => {
          this.tableData = res.data.data;

          console.log(res, "search");
          
        })
        .catch(err => {});
      //
    },
    viewdetail(index, row) {
      console.log(index, "index");
      this.dialogDetailsVisible = true;
      this.detailForm = row;
    },
    //////
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

    editgsForm(logID) {
      // console.log(logID,"zunbei")
      // this.$router.push("/LogDetails",logID);
    },
    getDa() {
      this.$axios
        .post("/oms-basic/abilityLog!selectLog.json", {
          level: "移动"
        })
        .then(res => {
          this.tableData = res.data.data;
          this.total = res.data.count;
          console.log(res.data.data);
          console.log(res, ".data.data");
        });
    },
    dao() {
      this.$axios
        .post("/oms-basic/abilityLog!exportLogMessage.json")
        .then(res => {
          this.goDownload(res.data.address)
        });
    },
    goDownload(_url){
      window.location.href="http://192.168.1.203:28084/oms-basic"+_url
    }
  },
  
  mounted() {
    this.getDa();
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
.home {
  height: 870px;
  background: #fff;
  overflow: hidden;
  position: relative;
  .titleQ {
    width: 100%;
    height: 50px;
    border-bottom: 1px solid #ccc;
    line-height: 50px;
    text-indent: 1em;
    border-left: 8px solid #3f67e1;
    box-sizing: border-box;
  }
  .search {
    padding: 12px 0 0 12px;
  }
  .block {
    position: absolute;
    left: 30%;
    bottom: 5px;
  }
}
/deep/.el-dialog__title {
  display: inline-block;
  width: 100%;
  text-align: center;
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
.detailBox {
  li {
    border-top: 1px solid #000;
    border-left: 1px solid #000;
    display: flex;
    align-items: center;
    justify-content: space-between;
    .bg_cyan {
      line-height: 40px;
      background-color: #f9fbfd;
      width: 100px;
      font: 14px/40px "";
      text-align: center;
      border-right: 1px solid #000;
    }
    .msgBox {
      flex: 1;
      line-height: 40px;
      text-indent: 30px !important;
      border-right: 1px solid #000;
    }
  }
}
</style>
