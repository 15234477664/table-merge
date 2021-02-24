# table-merge
element table 根据属性值合并行

```html
<template>
 <el-table
    ref="multipleTable"
    border
    show-summary
    :span-method="objectSpanMethod"
    :data="tableData"
    style="width: 100%;"
  >
    <el-table-column label="目录" prop="libName" align="center"></el-table-column>
    <el-table-column label="课程" prop="courseName" align="center"></el-table-column>
    <el-table-column label="数量（个）" prop="number" width="180px" align="center"></el-table-column>
    <el-table-column label="容量（M）" align="center" prop="capacity"></el-table-column>
    <el-table-column label="点击率（次）" align="center" prop="ctr"></el-table-column>
    <el-table-column label="下载量（次）" align="center" prop="downloads"></el-table-column>
    <el-table-column label="评星（分）" align="center" prop="score">
      <template slot-scope="scope">
        <el-rate v-model="scope.row.score" disabled text-color="#ff9900"></el-rate>
      </template>
    </el-table-column>
  </el-table>
</template>
```
```js
export default {
  computed: {},
  data() {
    return {
      tableData: [
        {
          libName: "计算机",
          courseName: "软件技术",
          number: 20,
          capacity: 34,
          ctr: 456656,
          downloads: 4343545,
          score: 5
        },
        {
          libName: "计算机",
          courseName: "软件技术",
          number: 20,
          capacity: 34,
          ctr: 456656,
          downloads: 4343545,
          score: 5
        },
        {
          libName: "学前教育",
          courseName: "幼儿心理学",
          number: 20,
          capacity: 334,
          ctr: 456656,
          downloads: 4343545,
          score: 4
        },
        {
          libName: "学前教育",
          courseName: "幼儿心理学",
          number: 20,
          capacity: 334,
          ctr: 456656,
          downloads: 4343545,
          score: 4
        },

        {
          libName: "学前教育",
          price: 223,
          courseName: "学前",
          number: 10,
          capacity: 1000,
          ctr: 456656,
          downloads: 4343545,
          score: 3
        },
         {
          libName: "计算机",
          courseName: "软件技术",
          number: 20,
          capacity: 34,
          ctr: 456656,
          downloads: 4343545,
          score: 5
        },
        {
          libName: "计算机",
          courseName: "软件技术",
          number: 20,
          capacity: 34,
          ctr: 456656,
          downloads: 4343545,
          score: 5
        },
        {
          libName: "设计",
          courseName: "建筑设计",
          number: 200,
          capacity: 349,
          ctr: 456656,
          downloads: 4343545,
          score: 5
        },
        {
          libName: "设计",
          courseName: "服装设计",
          number: 20,
          capacity: 3451,
          ctr: 456656,
          downloads: 4343545,
          score: 4.5
        },
        {
          libName: "机电",
          courseName: "汽修",
          number: 20,
          capacity: 3451,
          ctr: 456656,
          downloads: 4343545,
          score: 4.5
        }
      ],
      // 合并的长度
      colSpanArr: [],
      // 合并开始的位置
      rowIndexArr: []
    };
  },
  mounted() {
    this.getColSpanData("libName", this.tableData);
  },
  methods: {
    //  获取合并行的位置和合并长度
    getColSpanData(prop, data) {
      let colLength = 1;
      data.forEach((element, index) => {
        if (index == 0) {
        // rowIndex开始合并的位置为0,合并单元格数量默认为1
          this.rowIndexArr.push(0);
          colLength = 1;
        } else {
     
          if (element[prop] == data[index - 1][prop]) {
             // 当相邻两条数据相等时，rowspan+1,即colLength+1
            colLength += 1;
           // 当最后一条数据与倒数第2条不等式，在colSpanArr中push(1)
            if (index == data.length - 1) {
              this.colSpanArr.push(colLength);
            }
          } else {
        //  相邻两条不等时push index和合并的长度
            this.colSpanArr.push(colLength);
            this.rowIndexArr.push(index);
            if (index == data.length - 1) {
              this.colSpanArr.push(1);
            }
            colLength = 1;
          }
        }
      });
      this.rowIndexArr.push(data.length);
        console.log(this.rowIndexArr,this.colSpanArr)
      //  结果分别是：
      //this.rowIndexArr:[0, 2, 5, 7, 9，10]
      //this.colSpanArr:[2, 3, 2, 2, 1]
    },
    // 合并单元格
    objectSpanMethod({ row, column, rowIndex, columnIndex }) {
      if (columnIndex === 0) {
        for (let i = 0; i < this.colSpanArr.length; i++) {
        //如果rowIndex和this.rowIndexArr[i]相等则开始合并，合并数量对应为this.colSpanArr[i]
          if (rowIndex === this.rowIndexArr[i]) {
            return {
              rowspan: this.colSpanArr[i],
              colspan: 1
            };
          } else {
            if (
              rowIndex > this.rowIndexArr[i] &&
              rowIndex < this.rowIndexArr[i + 1]
            ) {
              return {
              //被合并行则返回rowspan: 0, colspan: 0，
              //这里被合并行为1,3,4,6,8          
                rowspan: 0,
                colspan: 0
              };
            }
          }
        }
      }
    },
      // 上面方法的结果等同于下面这种写法
    objectSpanMethod({ row, column, rowIndex, columnIndex }) {
      if (columnIndex === 0) {        
        if (rowIndex == 0) {
          return {
            rowspan: 2,
            colspan: 1
          };
        } else if (rowIndex == 2) {
          return {
            rowspan: 3,
            colspan: 1
          };
        } else if (rowIndex == 5) {
          return {
            rowspan: 2,
            colspan: 1
          };
        } else if (rowIndex == 7) {
          return {
            rowspan: 2,
            colspan: 1
          };
        } else if (rowIndex == 9) {
          return {
            rowspan: 1,
            colspan: 1
          };
        } else {
          return {
            rowspan: 0,
            colspan: 0
          };
        }
      }
  }
};

```
