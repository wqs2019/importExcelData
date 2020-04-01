<template>
  <div class="app-container">
    <div>
      <div style="display:inline-block;">
        <label class="radio-label" style="padding-left:0;">导出文件名:</label>
        <el-input
          v-model="filename"
          placeholder="请输入导出文件名(默认为：excel.xlsx)"
          style="width:345px;"
          prefix-icon="el-icon-document"
        />
      </div>
      <el-button
        :loading="downloadLoading"
        style="margin:0 0 20px 20px;"
        type="primary"
        icon="el-icon-document"
        @click="handleDownload"
      >导出学生考勤信息</el-button>
      <el-button style="margin:0 0 20px 20px;" type icon="el-icon-document" @click="tip">导出学号显示问题</el-button>
    </div>
    <el-table
      id="table-data"
      v-loading="listLoading"
      :data="list"
      element-loading-text="Loading..."
      border
      fit
      highlight-current-row
    >
      <el-table-column label="学号" prop="studentId"></el-table-column>
      <el-table-column label="姓名" prop="studentName"></el-table-column>
      <el-table-column label="班级" prop="class"></el-table-column>
      <el-table-column label="缺勤" prop="absent"></el-table-column>
      <el-table-column label="事假" prop="affairLeave"></el-table-column>
      <el-table-column label="病假" prop="sickLeave"></el-table-column>
      <el-table-column label="迟到" prop="late"></el-table-column>
      <el-table-column label="早退" prop="earlyLeave"></el-table-column>
    </el-table>
  </div>
</template>

<script>
import FileSaver from "file-saver";
import XLSX from "xlsx";
export default {
  name: "ExportExcel",
  data() {
    return {
      list: null,//这里的list就是表格的内容，渲染出来之后才可以点击导出
      listLoading: true,
      downloadLoading: false,
      filename: "",
      autoWidth: true,
      bookType: "xlsx"
    };
  },
  created() {
  //这里可以用于获取表格的数据，或者成功之后就调用类似这样，然后表格就会渲染，前提是你的表格列名要对上
    this.list = 获取的数据
  },
  methods: {
    handleDownload() {
      this.downloadLoading = true;
      var wb = XLSX.utils.table_to_book(document.querySelector("#table-data"));//这里就是关联表格，表格的id就是table-data
      var wbout = XLSX.write(wb, {
        bookType: "xlsx",
        bookSST: true,
        type: "array"
      });
      try {
        console.log(this.filename);
        FileSaver.saveAs(
          new Blob([wbout], { type: "application/octet-stream" }),
          this.filename + ".xlsx" || "excel"
        );
      } catch (e) {
        if (typeof console !== "undefined") console.log(e, wbout);
      }
      this.downloadLoading = false;
      console.log("导出完成");
      return wbout;
    },
    tip() {
      this.$alert(
        "选择学号列，右键设置单元格格式，数字-->设置为数值，小数位数设置为0",
        "修改提示",
        {
          confirmButtonText: "确定",
          callback: action => {}
        }
      );
    }
  }
};
</script>

<style>
.radio-label {
  font-size: 14px;
  color: #606266;
  line-height: 40px;
  padding: 0 12px 0 30px;
}
</style>
