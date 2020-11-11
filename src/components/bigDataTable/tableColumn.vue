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
    maxWidth: {
      type: [String, Number],
      default: 80,
    },
  },
  data() {
    return {
      isTableColumn: true,
      childrenColumnList: [],
      columnData: {},
    };
  },
  mounted() {
    this.insertColumn();
    // console.log(this.$scopedSlots);
    // console.log(this.$slots);
  },
  beforeDestroy() {
    this.removeColumn();
  },
  computed: {
    parentNode() {
      let parent = this.$parent;
      while (parent && !parent.isTable && !parent.isTableColumn) {
        parent = parent.$parent;
      }
      return parent;
    },
  },
  watch: {
    title() {
      this.removeColumn();
      this.insertColumn();
    },
  },
  methods: {
    insertColumn() {
      const parent = this.parentNode;
      let columnIndex;
      this.columnData = {
        ...this.$props,
        childrenColumnList: this.childrenColumnList,
      };
      if (this.$parent.isTable) {
        columnIndex = [].indexOf.call(parent.$refs.hiddenColumns.children, this.$el);
      }
      if (this.$parent.isTableColumn) {
        parent.childrenColumnList.push(this.columnData);
      } else {
        parent.insertColumn(this.columnData, columnIndex);
      }
    },
    removeColumn() {
      const parent = this.parentNode;
      const index = parent.childrenColumnList.indexOf(this.columnData);
      if (index > -1) {
        parent.childrenColumnList.splice(index, 1);
      }
    },
  },
  render() {
    return (
      <div class="hidden">
        {this.$slots.default}
      </div>
    );
  },
};
</script>

<style scoped lang="less">

</style>
