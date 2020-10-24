<script>
export default {
  name: 'BdTableColumn',
  props: {
    title: {
      type: [String, Number],
    },
    prop: {
      type: [String, Number],
    },
    minWidth: {
      type: [String, Number],
      default: 80,
    },
  },
  data() {
    return {
      isTableColumn: true,
      childrenColumns: [],
      columnInfo: {},
    };
  },
  created() {
    this.columnInfo = {
      title: this.title,
      prop: this.prop,
      minWidth: this.minWidth,
      childrenColumns: this.childrenColumns,
      renderCell: this.renderCell,
      renderHeader: this.renderHeader,
    };
    Object.keys(this.$props).forEach((key) => {
      this.$watch(key, () => {
        this.columnInfo.title = this.title;
        this.columnInfo.prop = this.prop;
        this.columnInfo.minWidth = this.minWidth;
        this.columnInfo.childrenColumns = this.childrenColumns;
        this.columnInfo.renderCell = this.renderCell;
        this.columnInfo.renderHeader = this.renderHeader;
      });
    });
  },
  mounted() {
    const parent = this.getParent();
    const parentChildrenColumns = parent.childrenColumns;
    const { columnInfo, childrenColumns } = this;
    columnInfo.childrenColumns = childrenColumns;
    const index = parentChildrenColumns.indexOf(columnInfo);
    const indexInNode = this.getSlotIndex();
    if (index < 0) {
      parentChildrenColumns.splice(indexInNode, 0, columnInfo);
    }
  },
  beforeDestroy() {
    console.log(111);
    const parent = this.getParent();
    const parentChildrenColumns = parent.childrenColumns;
    const { columnInfo } = this;
    const index = parentChildrenColumns.indexOf(columnInfo);
    if (index > -1) {
      parentChildrenColumns.splice(index, 1);
    }
  },
  methods: {
    renderHeader(data) {
      return this.$scopedSlots.header ? this.$scopedSlots.header({
        column: {
          title: this.title,
          prop: this.prop,
        },
      }) : this.title;
    },
    renderCell(data) {
      let render = this.$slots.default || data[this.prop];
      render = this.$scopedSlots.default ? this.$scopedSlots.default({
        row: data,
        column: {
          title: this.title,
          prop: this.prop,
        },
      }) : render;
      return render;
    },
    getParent() {
      let parent = this.$parent;
      while (!parent.isTable && !parent.isTableColumn && parent.$parent) {
        parent = parent.$parent;
      }
      return parent;
    },
    getSlotIndex() {
      const parent = this.getParent();
      const slotList = [...parent.$refs.hiddenColumn.children];
      return slotList.indexOf(this.$el);
    },
  },
  render() {
    return (
      <div className="hidden-column" ref="hiddenColumn">{this.$slots.default}</div>
    );
  },
};
</script>
<style lang="less" scoped>
.hidden-column{
  display: none;
}
</style>
