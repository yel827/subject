<template>
  <div class="home">
    <div class="titleQ">监控报告</div>
    <div class="search">
      <el-form :inline="true" :model="formInline" class="demo-form-inline">
        <span class="demonstration">资源类型</span>
        <el-select v-model="values" filterable placeholder="请选择" class="right">
          <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.label"
          ></el-option>
        </el-select>

        <span class="demonstration">资源状态</span>
        <el-select v-model="value" filterable placeholder="请选择" class="right">
          <el-option
            v-for="item in optionss"
            :key="item.label"
            :label="item.label"
            :value="item.label"
          ></el-option>
        </el-select>
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
          <el-button type="primary" @click="suo" class="right">生成报告</el-button>
        </el-form-item>
      </el-form>
    </div>
    <el-table
      :data="tableData.slice((currentPage-1)*pagesize,currentPage*pagesize)"
      style="width: 100%;"
      class="tabP"
    >
      <el-table-column prop="id" label="报告ID" width="180"></el-table-column>
      <el-table-column prop="typeName" label="资源类型" width="180"></el-table-column>
      <el-table-column prop="reportStartTime" label="报告开始时间"></el-table-column>
      <el-table-column prop="reportEndTime" label="报告结束时间"></el-table-column>
      <el-table-column show-overflow-tooltip prop="fileName" label="报告文件名"></el-table-column>
      <el-table-column prop="createTime" label="报告创建时间"></el-table-column>
      <el-table-column prop="status" label="报告状态"></el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button type="text" @click="open(scope.row.fileName)">
            <i class="icon iconfont icon-xiazai" style="font-size:18px; font-weight:bold;"></i>
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <div class="block">
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page.sync="currentPage1"
        :page-size="10"
        layout="total, prev, pager, next"
        :total="total"
      ></el-pagination>
    </div>
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
      optionss: [
        {
          value: "选项1",
          label: ""
        },
        {
          value: "选项1",
          label: "0"
        },
        {
          value: "选项2",
          label: "1"
        }
      ],
      options: [
        {
          value: "选项1",
          label: ""
        },
        {
          value: "选项1",
          label: "主机"
        },
        {
          value: "选项2",
          label: "服务"
        },
        {
          value: "选项2",
          label: "组件"
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
      ta:[]
    };
  },
  methods: {
    //数据展示
    getss() {
      this.$axios.post("/oms-basic/report!list.json").then(res => {
        console.log(res, "监控报告");
        this.tableData = res.data.list;
        this.total = res.data.count;
        // var arrs = []
        // for(var i=0; i<this.tableData.typeName.length; i++){
        //   for(var j=i+1; j<this.tableData.typeName.length; j++){
        //     if(arrs[i].indexOf(this.tableData.typeName[i])==-1){
        //       this.arrs.push(this.tableData.typeName[i])
        //     }
        //   }
        // }
        // this.ta = arrs
        // console.log(this.ta,'this.ta')
      });
    },

    //////
    open(index) {
      this.$confirm("此操作将删除该数据, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
        center: true
      })
        .then(() => {
          var dataL={
            fileName:index
          }
          this.$axios.post('/oms-basic/downloadSystemReportFile.json',this.$qs.stringify(dataL))
          .then(res=>{
            console.log(res,"下载")
          })
          this.$message({
            type: "success",
            message: "下载成功!"
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

    editgsForm(val) {
      this.$router.push("/LogDetails");
    },
    saveEditForm(aaa) {
      this.$refs[aaa].validate(valid => {
        console.log(this.$refs[aaa]);
        if (valid) {
          // this.$axios.put(`http://localhost:3000/admin/categories/${this.editForm.id}`,this.editForm).then( res =>{
          //   alert('更新成功');
          this.dialogEditgsVisible = false;
          this.init();
          console.log(valid);
          // })
        }
      });
    },
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
          this.value2 ||
          this.value ||
          this.values
        )
      ) {
        return;
      }
      var formData = {};
      // this.$data

      if (this.formInline.level) {
        formData.level = this.formInline.level;
      }
      if (this.formInline.name) {
        formData.source = this.formInline.name;
      }
      console.log(this.value2, "this.value2");

      if (this.value2 != "" && this.value2 != undefined) {
        formData.reportStartTime = this.dateTransfer(this.value2[0]);
        formData.reportEndTime = this.dateTransfer(this.value2[1]);
      }
      if (this.values) {
        formData.typeName = this.values;
      }
      if (this.value) {
        formData.status = this.value;
      }
      console.log(formData, "传递的值");
      this.$axios
        .post(
          "/oms-basic/report!list.json",
          this.$qs.stringify(formData)
        )
        .then(res => {
          this.tableData = res.data.list;
          
          
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
   
   
  },
  mounted() {
    this.getss();

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
</style>
