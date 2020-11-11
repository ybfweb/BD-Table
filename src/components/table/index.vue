<script>
export default {
  name: 'BdTable',
  props: {
    dataList: {
      type: Array,
      default: () => ([]),
    },
  },
  data() {
    return {
      isTable: true,
      childrenColumns: [],
    };
  },
  mounted() {
    // console.log(this.tableHeader);
  },
  methods: {
  },
  computed: {
    tableColumns() {
      const { childrenColumns } = this;
      const columnList = [];
      // 获取表头的深度 表头拍平
      let deep = 1;
      const allColumn = [...childrenColumns];
      const getDeep = (list = []) => {
        let has = false;
        const curDeep = deep;
        list.forEach((itemTmp) => {
          const item = itemTmp;
          item.deep = curDeep;
          let colSpan = 0;
          if (item.childrenColumns.length) {
            allColumn.push(...item.childrenColumns);
            if (!has) {
              has = true;
              deep += 1;
            }
            getDeep(item.childrenColumns);
            item.childrenColumns.forEach((cItem) => {
              colSpan += cItem.colSpan;
            });
          } else {
            columnList.push(item);
            colSpan = 1;
          }
          item.colSpan = colSpan;
        });
      };
      getDeep(childrenColumns);
      const titleList = [];
      for (let i = 0; i < deep; i += 1) {
        titleList.push([]);
      }
      // 表头分组
      allColumn.forEach((itemTmp) => {
        const item = itemTmp;
        if (!item.childrenColumns.length) {
          item.rowSpan = deep - item.deep + 1;
        } else {
          item.rowSpan = 1;
        }
        titleList[item.deep - 1].push(item);
      });
      return {
        titleList,
        columnList,
      };
    },

  },
  render() {
    const tableRender = (
      <table class="table">
        <thead>
        {
          this.tableColumns.titleList.map((item) => (
              <tr>
                {item.map((cItem) => (
                  <th
                    rowspan={cItem.rowSpan}
                    colspan={cItem.colSpan}>{cItem.renderHeader()}</th>
                ))}
              </tr>
          ))
        }
        </thead>
        <tbody>
        {
          this.dataList.map((item) => (
              <tr>
                {
                  this.tableColumns.columnList.map((cItem) => (
                    <td>{cItem.renderCell(item)}</td>
                  ))
                }
              </tr>
          ))
        }
        </tbody>
      </table>
    );
    return (
      <div class="bd-table">
        {tableRender}
        <div class="hidden-column" ref="hiddenColumn">{this.$slots.default}</div>
      </div>
    );
  },
};
</script>

<style lang="less" scoped>
.bd-table {
  background-color: #fff;
  overflow: auto;
  .table{
    border-collapse: collapse;
    min-width: 100%;
    th,td{
      border: 1px solid #e7e7e7;
      text-align: center;
      white-space: nowrap;
      padding: 8px;
    }
  }
  .hidden-column{
    display: none;
  }
}
</style>
