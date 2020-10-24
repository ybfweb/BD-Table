<script>
export default {
  name: 'BdTable',
  data() {
    return {
      isTable: true,
      childrenColumns: [],
    };
  },
  mounted() {
    console.log(this.tableHeader);
  },
  methods: {
  },
  computed: {
    tableHeader() {
      const columnList = this.childrenColumns;
      // 获取表头的深度 表头拍平
      let deep = 1;
      const allColumn = [...columnList];
      const getDeep = (list = []) => {
        let has = false;
        const curDeep = deep;
        list.forEach((itemTmp) => {
          const item = itemTmp;
          item.deep = curDeep;
          item.colSpan = item.childrenColumns.length || 1;
          if (item.childrenColumns.length) {
            allColumn.push(...item.childrenColumns);
            if (!has) {
              has = true;
              deep += 1;
            }
            getDeep(item.childrenColumns);
          }
        });
      };
      getDeep(columnList);
      const titleList = [];
      for (let i = 0; i < deep; i += 1) {
        titleList.push([]);
      }
      // 表头分组
      allColumn.forEach((itemTmp) => {
        const item = itemTmp;
        item.rowSpan = deep - item.deep + 1;
        titleList[item.deep - 1].push(item);
      });
      console.log(titleList);
      return titleList;
    },
  },
  render() {
    const tableRender = (
      <table class="table">
        <thead>
        {
          this.tableHeader.map((item) => (
              <tr>
                {item.map((cItem) => (
                  <th rowspan={cItem.rowSpan} colspan={cItem.colSpan}>{cItem.title}</th>
                ))}
              </tr>
          ))
        }
        </thead>
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
  .table{
    border-collapse: collapse;
    width: 100%;
    th,td{
      border: 1px solid #e7e7e7;
      text-align: center;
    }
  }
  .hidden-column{
    display: none;
  }
}
</style>
