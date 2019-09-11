<template>
  <div class="hello">
    源文件：
    <input type="file" @change="onFileChangeSrc" webkitdirectory />
    目标文件：
    <input type="file" @change="onFileChangeDest" />
    <button @click="confirm">生成</button>
    <div class="container" ref="sheetContainer"></div>
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
    return {};
  },
  methods: {
    onFileChangeSrc(e) {
      console.log(e);
      let files = e.target.files;
      for (let file of files){
        let reader = new FileReader();
        reader.onload = e => {
          let data = new Uint8Array(e.target.result);
          let workbook = XLSX.read(data, { type: "array" });
          console.log(workbook);
          let SheetNames = workbook.SheetNames;

          SheetNames.forEach(name => {
           let worksheet = workbook.Sheets[name];

          })

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
        console.log(workbook);

        let first_sheet_name = workbook.SheetNames[0];
        let address_of_cell = "A1";

        /* Get worksheet */
        let worksheet = workbook.Sheets[first_sheet_name];

        let worksheetHTML = XLSX.utils.sheet_to_html(worksheet);

        this.$refs["sheetContainer"].innerHTML = worksheetHTML;

        console.log(this.sheetHtml);

        /* Find desired cell */
        let desired_cell = worksheet[address_of_cell];

        /* Get the value */
        let desired_value = desired_cell ? desired_cell.v : undefined;

        /* DO SOMETHING WITH workbook HERE */
      };
      reader.readAsArrayBuffer(f);
    },
    confirm(){
      
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
