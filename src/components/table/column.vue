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
      columnInfo: {
        title: this.title,
        prop: this.prop,
        minWidth: this.minWidth,
        childrenColumns: [],
      },
    };
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
    const parent = this.getParent();
    const parentChildrenColumns = parent.childrenColumns;
    const { columnInfo } = this;
    const index = parentChildrenColumns.indexOf(columnInfo);
    if (index > -1) {
      parentChildrenColumns.splice(index, 1);
    }
  },
  methods: {
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
