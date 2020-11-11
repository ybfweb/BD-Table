<!--<template>
  <div
    class="big-data-table-box"
    :style="{maxHeight: maxHeight}"
    @scroll="tableScroll"
  >
    <div
      class="table-box"
      ref="tableBox"
      :style="{
        height: tableRealHeight + 'px',
        paddingTop: scrollTop + 'px',
      }"
    >
      <table
        class="big-data-table"
        ref="table"
      >
        <thead v-if="0">
        <tr
          v-for="(itemTr,index) in titleList"
          :key="index"
        >
          <th
            v-for="(item, tdIndex) in itemTr"
            :key="item.title + tdIndex"
            :width="item.maxWidth"
            :colspan="item.colSpan"
            :rowspan="item.rowSpan"
          >{{item.title}}
          </th>
        </tr>
        </thead>
        <tbody>
        <tr
          v-for="(item,index) in visibleColumn"
          :key="index"
        >
          <td
            v-for="(tdItem, tdIndex) in columns"
            :key="tdIndex"
            v-if="!tdItem.colspan || !tdItem.rowSpan"
            :colspan="tdItem.colspan"
            :rowspan="tdItem.rowSpan"
          >{{item[tdItem.prop]}}
          </td>
        </tr>
        </tbody>
      </table>
    </div>
    <div ref="hiddenColumns" class="hidden-columns">
      <slot></slot>
    </div>
  </div>
</template>-->
<script>

export default {
  name: 'BdTable',
  props: {
    data: {
      type: Array,
      default: () => ([]),
    },
    maxHeight: {
      type: [String, Number],
      default: 'auto',
    },
    visibleLimit: {
      type: [String, Number],
      default: 20,
    },
    spanMethod: {
      type: Function,
      default: () => ({ colspan: 1, rowspan: 1 }),
    },
  },
  data() {
    return {
      isTable: true,
      childrenColumnList: [],
      tableRealHeight: 0,
      offsetStart: 0,
      offsetEnd: 0,
      tdHeight: 0,
      scrollTop: 0,
    };
  },
  created() {
    this.offsetEnd = this.visibleLimit;
  },
  computed: {
    titleList() {
      return this.buildTableTitle();
    },
    columns() {
      return this.buildColumn();
    },
    tableData() {
      return this.data;
    },
    visibleColumn() {
      // const spanMethod = this.spanMethod;
      const list = this.tableData.slice(this.offsetStart, this.offsetEnd);
      return list;
    },
  },
  watch: {
    data: {
      immediate: true,
      handler() {
        this.$nextTick(() => {
          this.initVirtualScroll();
        });
      },
    },
  },
  methods: {
    insertColumn(data, index) {
      if (typeof index !== 'undefined') {
        this.childrenColumnList.splice(index, 0, data);
      } else {
        this.childrenColumnList.push(data);
      }
    },
    buildColumn() {
      const doFlattenColumns = (columns) => {
        const result = [];
        columns.forEach((column) => {
          if (column.childrenColumnList && column.childrenColumnList.length) {
            result.push(...doFlattenColumns(column.childrenColumnList));
          } else {
            result.push(column);
          }
        });
        return result;
      };
      return doFlattenColumns(this.childrenColumnList);
    },
    buildTableTitle() {
      // 计算表头层级深度 （colspan跨列 ，rowspan跨行）
      // 合并行 rowspan
      // 合并列 colspan
      let maxLevel = 1;
      const getDeep = (objTmp, parentTmp) => {
        const obj = objTmp;
        const parent = parentTmp;
        if (parent && !parent.level) {
          parent.level = 1;
        }
        const parentLevel = (parent && parent.level) || 0;
        obj.level = parentLevel + 1;
        if (obj.level > maxLevel) {
          maxLevel = obj.level;
        }
        if (obj.childrenColumnList && obj.childrenColumnList.length) {
          let colSpan = 0;
          obj.childrenColumnList.forEach((item) => {
            getDeep(item, obj);
            colSpan += item.colSpan;
          });
          obj.colSpan = colSpan;
        } else {
          obj.colSpan = 1;
        }
      };
      this.childrenColumnList.forEach((item) => {
        getDeep(item);
      });
      const getAllColumns = (columns) => {
        const result = [];
        columns.forEach((column) => {
          if (column.childrenColumnList) {
            result.push(column);
            result.push(...getAllColumns(column.childrenColumnList));
          } else {
            result.push(column);
          }
        });
        return result;
      };
      // 转换成多行
      const allColumnList = getAllColumns(this.childrenColumnList);
      const rows = [];
      for (let i = 0; i < maxLevel; i += 1) {
        rows.push([]);
      }
      allColumnList.forEach((columnTmp) => {
        const column = columnTmp;
        if (!column.childrenColumnList || !column.childrenColumnList.length) {
          column.rowSpan = (maxLevel - column.level) + 1;
        } else {
          column.rowSpan = 1;
        }
        rows[column.level - 1].push(column);
      });
      return rows;
    },
    initVirtualScroll() {
      const firstTd = this.$el.querySelector('td');
      // console.log(firstTd.offsetHeight);
      if (firstTd) {
        this.tdHeight = firstTd.offsetHeight;
        this.tableRealHeight = this.tableData.length * firstTd.offsetHeight;
      }
    },
    // tableScroll
    tableScroll(e) {
      const { scrollTop } = e.target;
      const { tdHeight } = this;
      // 获取屏幕中央数据的偏移
      const topLineInScreen = parseInt(scrollTop / tdHeight, 10);
      // console.log(topLineInScreen);
      const offsetStart = topLineInScreen - this.visibleLimit;
      const offsetEnd = topLineInScreen + (this.visibleLimit * 2);
      this.offsetStart = offsetStart > 0 ? offsetStart : 0;
      this.offsetEnd = offsetEnd > this.tableData.length ? this.tableData.length : offsetEnd;
      this.scrollTop = this.tdHeight * this.offsetStart;
    },
  },
  render() {
    const renderHeader = (
      <thead>
        {
          this.titleList.map((itemTr, indexTr) => (
            <tr key={indexTr}>
              {
                itemTr.map((item, tdIndex) => (
                  <th
                    key={item.title + tdIndex}
                    width={item.maxWidth}
                    colSpan={item.colSpan}
                    rowSpan={item.rowSpan}
                  >
                    {item.title}
                  </th>
                ))
              }
            </tr>
          ))
        }
      </thead>
    );
    const renderBody = (
      <tbody>
        {
          this.visibleColumn.map((item, index) => (
            <tr
              key={index}
            >
              {
                this.columns.map((tdItem, tdIndex) => {
                  const spanInfo = this.spanMethod({
                    row: item,
                    column: tdItem,
                    rowIndex: index,
                    columnIndex: tdIndex,
                  });
                  if (!spanInfo.rowspan || !spanInfo.colspan) {
                    return null;
                  }
                  return (
                    <td
                      key={tdIndex}
                      rowspan={spanInfo.rowspan}
                      colspan={spanInfo.colspan}
                    >
                      {item[tdItem.prop]}
                    </td>
                  );
                })
              }
            </tr>
          ))
        }
      </tbody>
    );
    return (
      <div
        class="big-data-table-box"
        style={{ maxHeight: this.maxHeight }}
        onScroll={this.tableScroll}
      >
        <div
          class="table-box"
          ref="tableBox"
          style={{
            height: `${this.tableRealHeight}px`,
            paddingTop: `${this.scrollTop}px`,
          }}
        >
          <table
            class="big-data-table"
            ref="table"
          >
            {false ? renderHeader : ''}
            {renderBody}
          </table>
        </div>
        <div ref="hiddenColumns" class="hidden-columns">
          {this.$slots.default}
        </div>
      </div>
    );
  },
};
</script>

<style scoped lang="less">
.big-data-table-box {
  overflow: auto;

  .table-box {
    background-color: #fff;
  }
}

.big-data-table {
  border-collapse: collapse;
  border: 1px solid #e7e7e7;
  background-color: #fff;
  width: 100%;

  td,th {
    border: 1px solid #e7e7e7;
    padding: 10px;
  }
}

.hidden-columns {
  display: none;
}
</style>
