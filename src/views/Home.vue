<template>
  <div class="home">
    <bd-table :data="tableData">
      <bd-table-column title="姓名" prop="userName"></bd-table-column>
      <bd-table-column title="地址">
        <bd-table-column title="省市区">
          <bd-table-column title="省" prop="province"></bd-table-column>
          <bd-table-column title="市" prop="city"></bd-table-column>
          <bd-table-column title="区" prop="county"></bd-table-column>
        </bd-table-column>
        <bd-table-column title="邮编" prop="zip"></bd-table-column>
      </bd-table-column>
      <bd-table-column title="大区" prop="region"></bd-table-column>
    </bd-table>
  </div>
</template>

<script>

import BdTable from '@/components/table/index.vue';
import BdTableColumn from '@/components/table/column.vue';
import Mock from 'mockjs';

export default {
  name: 'Home',
  components: { BdTableColumn, BdTable },
  data() {
    return {
      tableData: [],
    };
  },
  created() {
    const data = Mock.mock({
      'list|100': [{
        'id|+1': 1,
        userName: '@cname',
        province: '@province',
        city: '@city',
        county: '@county',
        region: '@region',
        zip: '@zip',
      }],
    });
    this.tableData = data.list;
  },
  methods: {

  },
};
</script>
<style lang="less" scoped>
.home{
  padding: 20px;
  background-color: #42b983;
}
</style>
