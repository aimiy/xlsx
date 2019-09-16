<template>
  <div class="hello">
    源文件：
    <input type="file" @change="onFileChangeSrc" webkitdirectory />
    <!-- 目标文件： -->
    <!-- <input type="file" @change="onFileChangeDest" /> -->
    <button @click="confirm">生成</button>
    <!-- <div class="container" ref="sheetContainer"></div> -->
  </div>
</template>

<script>
import XLSX from "xlsx";

export default {
  name: "HelloWorld",
  props: {
    msg: String
  },
  data() {
    return {
      srcWorkBooks: {}, // 全部文件表格数据
      srcSheets: [], // 全部文件sheets数据
      srcSheetsJson: [], // 全部文件json数据
      srcWorkBooksdealStatus:false, // json数据处理状态
      destWorkBook: []
    };
  },
  methods: {
    onFileChangeSrc(e) {
      let files = e.target.files;
      for (let file of files) {
        let reader = new FileReader();
        reader.onload = e => {
          let data = new Uint8Array(e.target.result);
          let workbook = XLSX.read(data, { type: "array" });
          this.srcWorkBooks[file.name] = workbook;
          let SheetNames = workbook.SheetNames;
          SheetNames.forEach(name => {
            let worksheet = workbook.Sheets[name];
            this.srcSheets.push(worksheet);
            let worksheetJdon = XLSX.utils.sheet_to_json(worksheet);
            this.srcSheetsJson.push(...worksheetJdon);
          });
        };
        reader.readAsArrayBuffer(file);
      }
    },
    onFileChangeDest(e) {
      let files = e.target.files,
        f = files[0];
      let reader = new FileReader();
      reader.onload = e => {
        let data = new Uint8Array(e.target.result);
        let workbook = XLSX.read(data, { type: "array" });
        this.destWorkBook = workbook;
        let first_sheet_name = workbook.SheetNames[0];
        let address_of_cell = "A1";
        let worksheet = workbook.Sheets[first_sheet_name];
        this.showSheet(worksheet);
        let desired_cell = worksheet[address_of_cell];
        let desired_value = desired_cell ? desired_cell.v : undefined;
      };
      reader.readAsArrayBuffer(f);
    },
    confirm() {
      let allDataSheet = XLSX.utils.json_to_sheet(this.srcSheetsJson);
      let allDataBook = XLSX.utils.book_new();
      XLSX.utils.book_append_sheet(allDataBook, allDataSheet, "汇总表");
      XLSX.writeFile(allDataBook, "汇总表.xlsx");
    },
    showSheet(sheetData) {
      let worksheetHTML = XLSX.utils.sheet_to_html(sheetData);
      this.$refs["sheetContainer"].innerHTML = worksheetHTML;
    }
  },
  mounted() {}
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
