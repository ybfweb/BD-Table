<template>
  <div>
    <h1>bd-table</h1>
    <div class="table">
      <bd-table
        :data="dataTable"
        :maxHeight="tableMaxHeight"
      >
        <bd-table-column title="ID" prop="id"></bd-table-column>
        <bd-table-column title="姓名" prop="name"></bd-table-column>
        <bd-table-column title="手机" prop="phone"></bd-table-column>
        <bd-table-column title="订单金额" prop="orderPrice"></bd-table-column>
        <bd-table-column title="邮箱" prop="email"></bd-table-column>
        <bd-table-column title="地址" prop="address">
          <bd-table-column title="省份" prop="province"></bd-table-column>
          <bd-table-column title="市" prop="city"></bd-table-column>
          <bd-table-column title="区" prop="county"></bd-table-column>
        </bd-table-column>
        <bd-table-column title="创建时间" prop="createTime"></bd-table-column>
      </bd-table>
    </div>
  </div>
</template>

<script>
import BdTable from '@/components/bigDataTable/table.vue';
import BdTableColumn from '@/components/bigDataTable/tableColumn.vue';
import Mock from 'mockjs';

export default {
  name: 'testIndexPage',
  components: { BdTableColumn, BdTable },
  data() {
    return {
      dataTable: [],
      tableMaxHeight: '500px',
    };
  },
  created() {
    const data = Mock.mock({
      // 属性 list 的值是一个数组，其中含有 1 到 10 个元素
      'list|150': [{
        id: '@increment',
        name: '@cname',
        phone: /^1[0-9]{10}$/,
        orderPrice: '@natural(1000, 100000)',
        email: '@email',
        province: '@province',
        county: '@county',
        city: '@city',
        address: '@zip',
        createTime: '@date("yyyy-MM-dd")',
      }],
    });
    // 输出结果
    // console.log(data.list);
    this.dataTable = Object.freeze(data.list || []);
    // console.log(this.dataTable);
  },
};
</script>

<style scoped lang="less">
.table{
  padding: 20px;
}
</style>
