<template>
  <div>
    <el-table
      :data="tableData"
      border
      style="width: 100%"
      v-loading="loading"
      key="table7"
    >
        <el-table-column label="序号" align="center" type="index" width="50"></el-table-column>
        <el-table-column prop="accountId" label="用户编号"></el-table-column>
        <el-table-column prop="phone" label="用户手机号"></el-table-column>
      <!-- 开团信息-->
      <el-table-column prop="name" label="用户信息" width="200">
        <template  #default="{ row }">
          <div v-if="row.name">姓名：{{row.name}}</div>
        </template>
      </el-table-column>
      <el-table-column prop="agentCode" label="代理人" width="200">
        <template  #default="{ row }">
          <div v-if="row.agent">
            <div v-if="row.agent.agentCode">工号：{{row.agent.agentCode}}</div>
            <div v-if="row.agent.agentName">姓名：{{row.agent.agentName}}</div>
            <div v-if="row.agent.agentPhone">手机号：{{row.agent.agentPhone}}</div>
            <div v-if="row.agent.agentOrg">机构：{{row.agent.agentOrg}}</div>
          </div>
        </template>
      </el-table-column>
      <el-table-column prop="qty" label="打卡步数"></el-table-column>
      <el-table-column prop="createTime" label="打卡时间"></el-table-column>      
    </el-table>    
  </div>
</template>

<script setup>
import { ref, defineProps, defineEmits,reactive} from 'vue';
import { ElMessage,ElCascader  } from 'element-plus'

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
const num = ref(0);
const dialogVisible = ref(false);
const dialogTableData = ref([]);
const state = reactive({
  loading: false
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