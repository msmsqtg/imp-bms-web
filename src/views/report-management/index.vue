<template>
  <div class="report-container">
    <!-- 报表选择区域 -->
    <el-card class="report-selector">
      <el-form :inline="true" :model="reportForm">
        <el-form-item label="选择活动">
          <el-select
            v-model="reportForm.activityId"
            placeholder="请选择活动"
            clearable
            style="width: 200px"
          >
            <el-option
              v-for="item in activityOptions"
              :key="item.id"
              :label="item.name"
              :value="item.id"
            />
          </el-select>
        </el-form-item>
        
        <el-form-item label="选择报表">
          <el-select
            v-model="reportForm.reportType"
            placeholder="请选择报表"
            clearable
            style="width: 200px"
            @change="handleReportChange"
          >
            <el-option
              v-for="item in reportTypeOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            />
          </el-select>
        </el-form-item>
      </el-form>
    </el-card>

    <!-- 报表名称 -->
    <div class="report-title">
      <h3>{{ currentReportName }}</h3>
    </div>

    <!-- 搜索条件 -->
    <el-card class="search-box">
      <el-form :inline="true" :model="searchForm">
        <el-form-item label="机构">
          <el-select
            v-model="searchForm.orgId"
            placeholder="请选择机构"
            clearable
            style="width: 150px"
          >
            <el-option
              v-for="item in orgOptions"
              :key="item.id"
              :label="item.name"
              :value="item.id"
            />
          </el-select>
        </el-form-item>
        
        <el-form-item label="用户手机号">
          <el-input
            v-model="searchForm.userPhone"
            placeholder="请输入手机号"
            clearable
            style="width: 180px"
          />
        </el-form-item>
        
        <el-form-item label="代理人工号">
          <el-input
            v-model="searchForm.agentId"
            placeholder="请输入工号"
            clearable
            style="width: 150px"
          />
        </el-form-item>
        
        <el-form-item label="组团编号">
          <el-input
            v-model="searchForm.groupId"
            placeholder="请输入组团编号"
            clearable
            style="width: 150px"
          />
        </el-form-item>
        
        <el-form-item label="创建时间">
          <el-date-picker
            v-model="searchForm.createTime"
            type="daterange"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            style="width: 280px"
          />
        </el-form-item>
        
        <el-form-item label="状态">
          <el-select
            v-model="searchForm.status"
            placeholder="请选择状态"
            clearable
            style="width: 120px"
          >
            <el-option
              v-for="item in statusOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            />
          </el-select>
        </el-form-item>
        
        <el-form-item>
          <el-button type="primary" @click="handleSearch">搜索</el-button>
          <el-button @click="resetSearch">重置</el-button>
          <el-button type="success" @click="handleExport">导出</el-button>
          <el-button type="info" @click="showExportRecords">导出记录</el-button>
        </el-form-item>
      </el-form>
    </el-card>

    <!-- 表格区域 -->
    <el-card class="table-box">
      <el-table
        :data="tableData"
        border
        style="width: 100%"
        v-loading="loading"
      >
        <el-table-column
          type="index"
          label="序号"
          width="60"
          align="center"
        />
        
        <template v-if="reportForm.reportType === 'groupList'">
          <el-table-column prop="groupCode" label="拼团编号" width="150" align="center" />
          <el-table-column prop="userPhone" label="用户手机号" width="130" align="center" />
          <el-table-column label="开团信息" width="180">
            <template #default="{ row }">
              <div>团长: {{ row.leaderName }}</div>
              <div>人数: {{ row.memberCount }}/{{ row.targetCount }}</div>
            </template>
          </el-table-column>
          <el-table-column prop="agentName" label="代理人" width="120" />
          <el-table-column prop="nickname" label="用户昵称" width="120" />
          <el-table-column prop="status" label="状态" width="100" align="center">
            <template #default="{ row }">
              <el-tag :type="getStatusTagType(row.status)">
                {{ getStatusLabel(row.status) }}
              </el-tag>
            </template>
          </el-table-column>
          <el-table-column prop="prizeType" label="奖品类型" width="120" />
          <el-table-column prop="prizeName" label="奖品名称" width="150" />
          <el-table-column prop="orderTime" label="订单时间" width="160" align="center" />
        </template>
        
        <template v-else-if="reportForm.reportType === 'groupLog'">
          <el-table-column prop="groupCode" label="拼团编号" width="150" align="center" />
          <el-table-column prop="userPhone" label="用户手机号" width="130" align="center" />
          <el-table-column prop="action" label="操作类型" width="120" />
          <el-table-column prop="actionTime" label="操作时间" width="160" align="center" />
          <el-table-column prop="agentName" label="代理人" width="120" />
          <el-table-column prop="nickname" label="用户昵称" width="120" />
          <el-table-column prop="status" label="状态" width="100" align="center">
            <template #default="{ row }">
              <el-tag :type="getStatusTagType(row.status)">
                {{ getStatusLabel(row.status) }}
              </el-tag>
            </template>
          </el-table-column>
        </template>
        
        <template v-else-if="reportForm.reportType === 'prizeList'">
          <el-table-column prop="prizeCode" label="奖品编号" width="150" align="center" />
          <el-table-column prop="prizeType" label="奖品类型" width="120" />
          <el-table-column prop="prizeName" label="奖品名称" width="200" />
          <el-table-column prop="quantity" label="数量" width="80" align="center" />
          <el-table-column prop="winnerPhone" label="中奖用户手机" width="130" align="center" />
          <el-table-column prop="winnerName" label="中奖用户" width="120" />
          <el-table-column prop="orderTime" label="订单时间" width="160" align="center" />
          <el-table-column prop="status" label="状态" width="100" align="center">
            <template #default="{ row }">
              <el-tag :type="getStatusTagType(row.status)">
                {{ getStatusLabel(row.status) }}
              </el-tag>
            </template>
          </el-table-column>
        </template>
        
        <el-table-column label="操作" width="120" fixed="right" align="center">
          <template #default="{ row }">
            <el-button type="text" size="small" @click="handleDetail(row)">详情</el-button>
          </template>
        </el-table-column>
      </el-table>
      
      <!-- 分页 -->
      <el-pagination
        class="pagination"
        v-model:current-page="pagination.currentPage"
        v-model:page-size="pagination.pageSize"
        :total="pagination.total"
        :page-sizes="[10, 20, 50, 100]"
        layout="total, sizes, prev, pager, next, jumper"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
      />
    </el-card>

    <!-- 导出记录弹窗 -->
    <el-dialog
      v-model="exportRecordVisible"
      title="导出记录"
      width="60%"
    >
      <el-table :data="exportRecords" border>
        <el-table-column prop="fileName" label="文件名" width="200" />
        <el-table-column prop="reportType" label="报表类型" width="120">
          <template #default="{ row }">
            {{ getReportTypeLabel(row.reportType) }}
          </template>
        </el-table-column>
        <el-table-column prop="createTime" label="导出时间" width="180" />
        <el-table-column prop="status" label="状态" width="100">
          <template #default="{ row }">
            <el-tag :type="row.status === 'success' ? 'success' : 'danger'">
              {{ row.status === 'success' ? '成功' : '失败' }}
            </el-tag>
          </template>
        </el-table-column>
        <el-table-column label="操作" width="120">
          <template #default="{ row }">
            <el-button
              type="text"
              size="small"
              :disabled="row.status !== 'success'"
              @click="downloadExportFile(row)"
            >
              下载
            </el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-dialog>
  </div>
</template>

<script setup>
import { ref, reactive, computed } from 'vue'
import { ElMessage } from 'element-plus'

// 报表表单
const reportForm = reactive({
  activityId: '',
  reportType: ''
})

// 报表类型选项
const reportTypeOptions = [
  { value: 'groupList', label: '拼团列表' },
  { value: 'groupLog', label: '拼团日志' },
  { value: 'prizeList', label: '奖品列表' }
]

// 当前报表名称
const currentReportName = computed(() => {
  const item = reportTypeOptions.find(item => item.value === reportForm.reportType)
  return item ? item.label : '请选择报表'
})

// 活动选项
const activityOptions = ref([
  { id: '1', name: '双十一拼团活动' },
  { id: '2', name: '年终大促拼团' },
  { id: '3', name: '新春佳节拼团' }
])

// 搜索表单
const searchForm = reactive({
  orgId: '',
  userPhone: '',
  agentId: '',
  groupId: '',
  createTime: [],
  status: ''
})

// 机构选项
const orgOptions = ref([
  { id: '1001', name: '北京分公司' },
  { id: '1002', name: '上海分公司' },
  { id: '1003', name: '广州分公司' }
])

// 状态选项
const statusOptions = ref([
  { value: '1', label: '进行中' },
  { value: '2', label: '已完成' },
  { value: '3', label: '已取消' },
  { value: '4', label: '已过期' }
])

// 表格数据
const tableData = ref([])
const loading = ref(false)

// 分页
const pagination = reactive({
  currentPage: 1,
  pageSize: 10,
  total: 0
})

// 导出记录相关
const exportRecordVisible = ref(false)
const exportRecords = ref([
  {
    id: '1',
    fileName: '拼团列表_20231115.xlsx',
    reportType: 'groupList',
    createTime: '2023-11-15 14:30:22',
    status: 'success',
    filePath: '/exports/groupList_20231115.xlsx'
  },
  {
    id: '2',
    fileName: '奖品列表_20231110.xlsx',
    reportType: 'prizeList',
    createTime: '2023-11-10 10:15:33',
    status: 'success',
    filePath: '/exports/prizeList_20231110.xlsx'
  }
])

// 获取状态标签
const getStatusLabel = (status) => {
  const item = statusOptions.value.find(item => item.value === status)
  return item ? item.label : status
}

// 获取状态标签类型
const getStatusTagType = (status) => {
  const map = {
    '1': 'primary',   // 进行中
    '2': 'success',   // 已完成
    '3': 'danger',    // 已取消
    '4': 'info'       // 已过期
  }
  return map[status] || ''
}

// 获取报表类型标签
const getReportTypeLabel = (type) => {
  const item = reportTypeOptions.find(item => item.value === type)
  return item ? item.label : type
}

// 报表类型变化
const handleReportChange = (val) => {
  if (val) {
    fetchTableData()
  } else {
    tableData.value = []
  }
}

// 搜索
const handleSearch = () => {
  pagination.currentPage = 1
  fetchTableData()
}

// 重置搜索
const resetSearch = () => {
  searchForm.orgId = ''
  searchForm.userPhone = ''
  searchForm.agentId = ''
  searchForm.groupId = ''
  searchForm.createTime = []
  searchForm.status = ''
}

// 获取表格数据
const fetchTableData = () => {
  if (!reportForm.reportType) {
    ElMessage.warning('请先选择报表类型')
    return
  }

  loading.value = true
  
  // 模拟API调用
  setTimeout(() => {
    // 根据报表类型生成模拟数据
    if (reportForm.reportType === 'groupList') {
      tableData.value = [
        {
          id: '101',
          groupCode: 'TG20231115001',
          userPhone: '13800138001',
          leaderName: '张团长',
          memberCount: 5,
          targetCount: 10,
          agentName: '王代理',
          nickname: '小张',
          status: '1',
          prizeType: '电子产品',
          prizeName: '智能手表',
          orderTime: '2023-11-15 10:30:22'
        },
        // 更多数据...
      ]
    } else if (reportForm.reportType === 'groupLog') {
      tableData.value = [
        {
          id: '201',
          groupCode: 'TG20231115001',
          userPhone: '13800138002',
          action: '参团',
          actionTime: '2023-11-15 11:15:33',
          agentName: '李代理',
          nickname: '小李',
          status: '1'
        },
        // 更多数据...
      ]
    } else if (reportForm.reportType === 'prizeList') {
      tableData.value = [
        {
          id: '301',
          prizeCode: 'PZ2023111001',
          prizeType: '电子产品',
          prizeName: '无线耳机',
          quantity: 1,
          winnerPhone: '13800138003',
          winnerName: '小王',
          orderTime: '2023-11-10 14:20:45',
          status: '2'
        },
        // 更多数据...
      ]
    }
    
    pagination.total = tableData.value.length
    loading.value = false
  }, 800)
}

// 分页大小变化
const handleSizeChange = (val) => {
  pagination.pageSize = val
  fetchTableData()
}

// 当前页变化
const handleCurrentChange = (val) => {
  pagination.currentPage = val
  fetchTableData()
}

// 导出
const handleExport = () => {
  if (!reportForm.reportType) {
    ElMessage.warning('请先选择报表类型')
    return
  }
  
  ElMessage.success('导出任务已提交，请稍后在导出记录中查看')
  
  // 模拟添加到导出记录
  const now = new Date()
  const timestamp = now.toISOString().replace(/[-:]/g, '').split('.')[0]
  exportRecords.value.unshift({
    id: Date.now().toString(),
    fileName: `${currentReportName.value}_${timestamp}.xlsx`,
    reportType: reportForm.reportType,
    createTime: now.toLocaleString(),
    status: 'success',
    filePath: `/exports/${reportForm.reportType}_${timestamp}.xlsx`
  })
}

// 显示导出记录
const showExportRecords = () => {
  exportRecordVisible.value = true
}

// 下载导出文件
const downloadExportFile = (record) => {
  ElMessage.success(`开始下载: ${record.fileName}`)
  // 实际项目中这里应该是文件下载逻辑
  console.log('下载文件:', record.filePath)
}

// 查看详情
const handleDetail = (row) => {
  ElMessage.info(`查看详情: ${row.id}`)
  // 实际项目中这里应该是跳转详情页或打开详情弹窗
}
</script>

<style scoped>
.report-container {
  padding: 20px;
}

.report-selector {
  margin-bottom: 20px;
}

.report-title {
  margin: 10px 0 20px;
  padding-left: 10px;
  border-left: 4px solid #409eff;
}

.search-box {
  margin-bottom: 20px;
}

.table-box {
  margin-bottom: 20px;
}

.pagination {
  margin-top: 20px;
  justify-content: flex-end;
}
</style>