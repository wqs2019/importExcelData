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
      >导出信息</el-button>
      <el-button style="margin:0 0 20px 20px;" type icon="el-icon-document" @click="tip">导出数据显示问题</el-button>
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
      <el-table-column label="列1" prop="属性1"></el-table-column>
      <el-table-column label="列2" prop="属性2"></el-table-column>
      <el-table-column label="列3" prop="属性3"></el-table-column>
      <el-table-column label="列4" prop="属性4"></el-table-column>
      <el-table-column label="列5" prop="属性5"></el-table-column>
      <el-table-column label="列6" prop="属性6"></el-table-column>
      <el-table-column label="列7" prop="属性7"></el-table-column>
      <el-table-column label="列8" prop="属性8"></el-table-column>
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
        "选择问题列，右键设置单元格格式，数字-->设置为数值，小数位数设置为0",
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
