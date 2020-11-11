<template>
  <div class="home">
    <bd-table :data-list="tableData">
      <bd-table-column title="姓名" prop="userName"></bd-table-column>
      <bd-table-column title="地址">
        <bd-table-column title="省市区">
          <bd-table-column key="province"
          title="省" prop="province"></bd-table-column>
          <bd-table-column title="市" prop="city"></bd-table-column>
          <bd-table-column v-if="show" title="区" prop="county"></bd-table-column>
          <!--<template v-for="item in tableTitle">
            <bd-table-column
              :key="item.prop"
              :title="item.title"
              :prop="item.prop"></bd-table-column>
          </template>-->
        </bd-table-column>
        <bd-table-column v-if="show" title="邮编" prop="zip">
          <template slot-scope="scope" slot="header">
            {{scope.column.title}}<button>1144</button>
          </template>
          <template slot-scope="scope">{{scope.row.region + scope.row.zip}}</template>
        </bd-table-column>
      </bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
      <bd-table-column v-if="show" title="大区" prop="region"></bd-table-column>
    </bd-table>
    <button @click="hideAddress">hide</button>
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
      tableTitle: [],
      show: true,
    };
  },
  created() {
    const data = Mock.mock({
      'list|20': [{
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
    setTimeout(() => {
      this.tableTitle = [
        { title: '省', prop: 'province' },
        { title: '市', prop: 'city' },
        { title: '区', prop: 'county' },
      ];
    }, 0);
  },
  methods: {
    hideAddress() {
      this.show = !this.show;
    },
  },
};
</script>
<style lang="less" scoped>
.home{
  padding: 20px;
  background-color: #42b983;
}
</style>
