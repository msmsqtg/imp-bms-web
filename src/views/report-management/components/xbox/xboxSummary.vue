<template>
  <div>
     <el-table
        :data="tableData"
        border
        style="width: 100%"
        v-loading="loading"
        key="table3"
      >
        <el-table-column
          type="index"
          label="编号"
          width="60"
          align="center"
        />
        <el-table-column prop="userName" label="上级机构"></el-table-column>
        <el-table-column prop="userName" label="支公司"></el-table-column>
        <el-table-column prop="code" label="参与业务员数"></el-table-column>
        <el-table-column prop="code" label="报名客户数"></el-table-column>
        <el-table-column prop="code" label="谢谢惠顾"></el-table-column>
        <el-table-column prop="org" label="奖项领奖数据" ></el-table-column>
        <el-table-column prop="phone" label="签到核销数"></el-table-column>
        <el-table-column prop="islogin" label="盲盒中奖数"></el-table-column>
      </el-table>
     <!-- 分页 -->
    <el-pagination
      class="pagination"
      v-model:current-page="currentPage"
      v-model:page-size="pageSize"
      :total="total"
      :page-sizes="[10, 20, 50, 100]"
      layout="total, sizes, prev, pager, next, jumper"
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
    />    
  </div>
</template>

<script setup>
import { ref, defineProps, defineEmits } from 'vue';

const props = defineProps({
  tableData: {
    type: Array,
    default: () => []
  },
  columns: {
    type: Array,
    required: true
  },
  loading: {
    type: Boolean,
    default: false
  },
  total: {
    type: Number,
    default: 0
  }
});
const emit = defineEmits(['page-change']);

const currentPage = ref(1);
const pageSize = ref(10);

// 分页大小变化
const handleSizeChange = (val) => {
  pageSize.value = val;
  emitPageChange();
};

// 当前页变化
const handleCurrentChange = (val) => {
  currentPage.value = val;
  emitPageChange();
};

// 触发分页变化事件
const emitPageChange = () => {
  emit('page-change', {
    page: currentPage.value,
    pageSize: pageSize.value
  });
};
</script>

<style scoped>
.pagination-container {
  margin-top: 20px;
  display: flex;
  justify-content: flex-end;
}
</style>