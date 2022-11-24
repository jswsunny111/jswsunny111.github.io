---
title: Element-ui 表格合并行
date: 2022-09-13 20:22:28
tags: Element
category: JavaScript
---

### 实现 element table 表格合并

![E_J_tableHb_1](/img/2022/E_J_tableHb_1.png)
饿了么框架介绍的基础，合并的主要属性是 span-method 方法 返回的 rowspan，colspan 值是做依赖合并的依据

```
 <ez-table
      :data="tableData"
      :span-method="objectSpanMethod"
      border
      style="width: 100%; margin-top: 20px">
      <ez-table-column
        prop="id"
        label="ID"
        width="180">
      </ez-table-column>
      <ez-table-column
        prop="name"
        label="姓名">
      </ez-table-column>
      <ez-table-column
        prop="amount1"
        label="数值 1（元）">
      </ez-table-column>
      <ez-table-column
        prop="amount2"
        label="数值 2（元）">
      </ez-table-column>
      <ez-table-column
        prop="amount3"
        label="数值 3（元）">
      </ez-table-column>
    </ez-table>

	data(){
		return {
			 tableData: [{
          id: '12987122',
          name: '王小虎',
          amount1: '234',
          amount2: '3.2',
          amount3: 10
        }, {
          id: '12987123',
          name: '王小虎',
          amount1: '165',
          amount2: '4.43',
          amount3: 12
        }, {
          id: '12987124',
          name: '王小虎',
          amount1: '324',
          amount2: '1.9',
          amount3: 9
        }, {
          id: '12987125',
          name: '王小虎',
          amount1: '621',
          amount2: '2.2',
          amount3: 17
        }, {
          id: '12987126',
          name: '王小虎',
          amount1: '539',
          amount2: '4.1',
          amount3: 15
        }]
		}
	},
	 methods: {
	    objectSpanMethod({ row, column, rowIndex, columnIndex }) {
          if (columnIndex === 0) {
           if (rowIndex % 2 === 0) {
            return {
              rowspan: 2,
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
	 }
```

#### 主要问题/解决方案

1. 当数据执行时，怎么动态判断需要合并或者合并行的行数，给 rowspan，colspan 做依据
2. 当需要合并时的依据 识别 ID 条件是什么？

判断首先合并的场景，是相邻的某个 key 值相同的情况下，进行合并。可以理解为当前这一条与上一条数据 是否相同进行合并
（PS：首先确保数据是否排序过或者一样的不在一起需不需要合并，注意处理边界线）
那么我们可以 给 数据加一个 count 值 去动态计算合并的行数，当满足条件给数值进行累加

#### 功能实现

##### 常规表格数据

![E_J_tableHb_3](/img/2022/E_J_tableHb_3.png)

```
//实际业务，以‘adviceNo’做识别key值比对，数据中只需要相邻的‘adviceNo’值一致的合并。
<ez-table
        border
        size="mini"
        :data="tabdata"
        :span-method="objectSpanMethod"
        ref="tableData"
        style="width: 100%"
        max-height="450px"
    >
        <ez-table-column
            align="center"
            prop="adviceNo"
            label="医嘱编号"
            width="90"
        >
        </ez-table-column>
        <ez-table-column
            align="center"
            prop="medicalTechName"
            label="项目"
            width="120"
        >
        </ez-table-column>
        <ez-table-column
            prop="name"
            align="center"
            label="部位"
        >
        </ez-table-column>
        <ez-table-column
            prop="technique"
            align="center"
            label="方法"
        ></ez-table-column>
        <ez-table-column
            width="100"
            prop="duration"
            align="center"
                label="时间"
        ></ez-table-column>
    </ez-table>
methods:{
	async getDetailInfo(){
		let res = await ****api***
		this.tabdata = this.mergeTableData(['adviceNo'],res)
	},
	 /** 处理合并行数据  */
        mergeTableData(mergeVals, tabs) {
            let spanArr = [],
                indexArr = 0;
            let tabData = JSON.parse(JSON.stringify(tabs));
            mergeVals.forEach((val) => {
                tabData.map((item, index) => {
                    if (index === 0) {
                        spanArr.push(1);
                        indexArr = 0;
                    } else {
                        // 判断是否与上一行数据相同
                        if (tabData[index][val] === tabData[index - 1][val]) {
                            spanArr[indexArr] += 1;
                            spanArr.push(1); //合并的当前数据
                        } else {
                            spanArr.push(1);
                            indexArr = index;
                        }
                    }
                });
            });
            this.spanArr = spanArr;
            return tabData;
        },
        /** 合并方法 */
        objectSpanMethod({ row, column, rowIndex, columnIndex }) {
            if (columnIndex === 0) {
                const _row = this.spanArr[rowIndex];// 这里获取拿到的处理好数据进行赋值
                const _col = _row > 0 ? 1 : 0;
                return {
                    rowspan: _row,
                    colspan: _col,
                };
            }
        },
}
```

##### 嵌套数据结构

相比比之前的常规的多一层数据嵌套，处理方式大致一样，赋值方面稍作处理，将值放到对应的数据中  
 ![E_J_tableHb_2](/img/2022/E_J_tableHb_2.png)

```
   //实际业务，以‘serialNo’做识别key值比对，数据中只需要相邻的‘serialNo’值一致的合并。数据结构是多个表格  [{[]},{[]}]
    <div v-for="(item, index) in detaildata" :key="index">
            <ez-table
                :header-cell-style="{
                    background: '#E4E8EF',
                    color: '#333',
                    fontWeight: 500,
                }"
                border
                size="mini"
                :data="item.treatmentList"
                ref="tableData"
                style="width: 100%"
                max-height="450px"
                :span-method="objectSpanMethod"
            >
                <ez-table-column
                    align="center"
                    prop="serialNo"
                    label="序号"
                    width="60"
                >
                </ez-table-column>
                <ez-table-column
                    align="center"
                    prop="date"
                    label="日期"
                    width="120"
                >
                </ez-table-column>
                <ez-table-column
                    prop="adviceNo"
                    align="center"
                    label="医嘱编号"
                    width="100"
                >
                </ez-table-column>
                <ez-table-column
                    prop="startTime"
                    align="center"
                    label="开始时间"
                    width="100"
                ></ez-table-column>
                <ez-table-column
                    prop="remark"
                    width="180"
                    align="center"
                    class="hh_txt"
                    label="备注"
                ></ez-table-column>
                <ez-table-column
                    prop="therapistName"
                    align="center"
                    width="100"
                    label="治疗者"
                ></ez-table-column>
            </ez-table>
        </div>
methods:{

	async getDetailInfo(){
		let res = await ****api***
		this.detaildata = this.filterData('serialNo',res)
	},
	 /** 处理合并行数据 */
        filterData(key,data) {
            let spanArr = [];
            let indexArr = [];
            let _sort = 0;
            let Data = JSON.parse(JSON.stringify(data));
            for (let index = 0; index < Data.length; index++) {
                let tableDate = Data[index].treatmentList;// 需要处理的表格数据
                for (let i = 0; i < tableDate.length; i++) {
                    if (i === 0) { // 处理第一条数据没有前一项内容
                        spanArr[index] = [];
                        indexArr[index] = [];
                        spanArr[index].push(1);
                        indexArr[index].push(1);
                        this.pos = 0;
                        _sort = 1;
                    } else {
                        // 判断当前元素与上一个元素是否相同;key为识别id
                        if (tableDate[i][key] === tableDate[i - 1][key]) {
                            spanArr[index][this.pos] += 1;
                            spanArr[index].push(0);
                            _sort = indexArr[index][i - 1];
                            indexArr[index].push(_sort);
                        } else {
                            spanArr[index].push(1);
                            this.pos = i;
                            _sort += 1;
                            indexArr[index].push(_sort);
                        }
                    }
                }
            }
			// 将值放入对应的数据中进行返回
            Data.forEach((item, index) => {
                item.treatmentList.forEach((val, k) => {
                    Data[index].treatmentList[k]._row = spanArr[index][k];
                    Data[index].treatmentList[k]._sort = indexArr[index][k];
                });
            });
            return Data;
        },
		/** 合并方法 */
        objectSpanMethod({ row, column, rowIndex, columnIndex }) {
            if (columnIndex === 0) { 	// columnIndex需要合并的那个列索引
                const _row = row._row;
                onst _col = _row > 0 ? 1 : 0;
                return {
                    rowspan: _row,
                    colspan: _col,
                };
            }
        },
}
```
