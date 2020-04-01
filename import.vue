
<template>
  <div class="app-container">
    <upload-excel-component :on-success="handleSuccess" :before-upload="beforeUpload" />
    <el-button type="primary" round @click="importDataInfo">
      一键导入数据库
      <i class="el-icon-upload el-icon--right"></i>
    </el-button>
    <el-table :data="tableData" border highlight-current-row style="width: 100%;margin-top:20px;">
      <el-table-column v-for="item of tableHeader" :key="item" :prop="item" :label="item" />
    </el-table>
  </div>
</template>

<script>
import UploadExcelComponent from "./index.vue";

export default {
  name: "UploadExcel",
  components: { UploadExcelComponent },
  data() {
    return {
      disabled: 0,
      tableData: [],
      tableHeader: [],
      tcId:''
    };
  },
  created(){
    this.tcId = this.$store.state.tcId;
    console.log(this.tcId)
  },
  methods: {
    beforeUpload(file) {
      const isLt1M = file.size / 1024 / 1024 < 1;

      if (isLt1M) {
        return true;
      }

      this.$message({
        message: "Excel文件上传不能大于1MB",
        type: "warning"
      });
      return false;
    },
    handleSuccess({ results, header }) {
      console.log(results);
      this.disabled = true;
      this.tableData = results;
      this.tableHeader = header;
    },
    importData(item) {
      let _this = this;
      const promise = new Promise(function(resolve, reject) {
        _this.$axios
          .get("/api/importDataToChooseCourse", {
            params: {
              studentId: item.studentId + "",
              tcId: _this.tcId,
            }
          })
          .then(res => {
            console.log(res);
            resolve(res);
          });
      });
      return promise;
    },
    importDataInfo() {
      console.log(this.tableData);
      if (this.tableData.length === 0) {
        this.$message.error("请先上传文件");
      } else {
        const loading = this.$loading({
          lock: true,
          text: "数据插入中...",
          spinner: "el-icon-loading",
          background: "rgba(0, 0, 0, 0.7)"
        });
        let tempPromise = [];
        this.tableData.forEach((item, index) => {
          tempPromise.push(this.importData(item));
          //  this.importData(item);
        });
        Promise.all(tempPromise).then(res => {
          console.log(res);
          loading.close();
          this.$message({
            message: "导入数据成功！",
            type: "success"
          });
        });
      }
    }
  }
};
</script>
