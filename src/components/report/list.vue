<template>
  <el-dialog
    v-model="dialogVisible"
    title="权限管理"
    width="80%"
    top="5vh"
    destroy-on-close
  >
    <!-- 搜索区域 -->
    <el-card shadow="never" class="search-box">
      <el-form :model="searchForm" inline>
        <el-form-item label="活动类型">
          <el-select 
            v-model="searchForm.activityType" 
            placeholder="请选择活动类型"
            clearable
            style="width: 200px"
          >
            <el-option
              v-for="item in activityTypeOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            />
          </el-select>
        </el-form-item>
        
        <el-form-item label="活动名称">        
          <el-select v-model="searchForm.activityId" placeholder="请选择活动名称">
            <el-option
              v-for="item in activityOptions"
              :key="item.id"
              :label="item.name"
              :value="item.id"
            />
          </el-select>
        </el-form-item>
        
        <el-form-item>
          <el-button type="primary" @click="handleSearch">搜索</el-button>
          <el-button @click="resetSearch">重置</el-button>
          <el-button type="success" @click="showAddDialog">新增报表权限</el-button>
        </el-form-item>
      </el-form>
    </el-card>

    <!-- 表格区域 -->
    <el-card shadow="never" class="table-box">
      <el-table :data="tableData" border v-loading="loading">
        <el-table-column type="index" label="序号" width="60" align="center" />
        <el-table-column prop="activityType" label="活动类型" width="120" align="center">
          <template #default="{ row }">
            {{ getActivityTypeLabel(row.activityType) }}
          </template>
        </el-table-column>
        <el-table-column prop="activityName" label="活动名称" min-width="150" />
        <el-table-column prop="createTime" label="创建时间" width="180" align="center" />
        <el-table-column label="操作" width="180" align="center" fixed="right">
          <template #default="{ row }">
            <el-button type="text" size="small" @click="handleEdit(row)">编辑</el-button>
            <el-button type="text" size="small" @click="handleDelete(row)" style="color: #f56c6c">
              删除
            </el-button>
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

    <!-- 新增/编辑弹窗 -->
    <el-dialog
      v-model="innerDialogVisible"
      :title="dialogTitle"
      width="50%"
      :close-on-click-modal="false"
    >
      <el-form :model="formData" :rules="rules" ref="formRef" label-width="120px">
        <el-form-item label="活动类型" prop="activityType">
          <el-select 
            v-model="formData.activityType" 
            placeholder="请选择活动类型"
            @change="handleActivityTypeChange"
          >
            <el-option
              v-for="item in activityTypeOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            />
          </el-select>
        </el-form-item>
        
        <el-form-item label="活动名称" prop="activityId">
          <el-select 
            v-model="formData.activityId" 
            placeholder="请选择活动名称"
            filterable
          >
            <el-option
              v-for="item in activityOptions"
              :key="item.id"
              :label="item.name"
              :value="item.id"
            />
          </el-select>
        </el-form-item>        
        <el-form-item label="权限报表" prop="permissions">
          <el-tree
            ref="treeRef"
            :data="permissionTree"
            show-checkbox
            node-key="id"
            :props="treeProps"
            :default-expand-all="true"
          />
        </el-form-item>
        <el-form-item label="报表权限" prop="type"> 
        <el-checkbox-group v-model="formData.type">
          <el-checkbox label="美食/餐厅线上活动" name="type"></el-checkbox>
          <el-checkbox label="地推活动" name="type"></el-checkbox>
          <el-checkbox label="线下主题活动" name="type"></el-checkbox>
          <el-checkbox label="单纯品牌曝光" name="type"></el-checkbox>
        </el-checkbox-group>
        </el-form-item>
      </el-form>
      
      <template #footer>
        <el-button @click="innerDialogVisible = false">取消</el-button>
        <el-button type="primary" @click="submitForm">确定</el-button>
      </template>
    </el-dialog>
  </el-dialog>
</template>

<script setup>
import { ref, reactive, computed, watch } from 'vue'
import { ElMessage, ElMessageBox } from 'element-plus'

const props = defineProps({
  modelValue: Boolean,
  roleId: String || Number // 可选的初始活动ID
})

const emit = defineEmits(['update:modelValue', 'success'])

// 控制对话框显示
const dialogVisible = computed({
  get() {
    return props.modelValue
  },
  set(value) {
    emit('update:modelValue', value)
  }
})

// 搜索表单
const searchForm = reactive({
  activityType: '',
  activityId:''
})

// 活动类型字典
const activityTypeOptions = [
  { value: '1', label: '线上活动' },
  { value: '2', label: '线下活动' },
  { value: '3', label: '混合活动' }
]

// 表格数据
const tableData = ref([
  {
    id: '1',
    activityType: '1',
    activityName: '双十一促销活动',
    createTime: '2023-10-15 14:30:22'
  },
  {
    id: '2',
    activityType: '2',
    activityName: '年度客户答谢会',
    createTime: '2023-09-20 10:15:33'
  }
])

// 分页
const pagination = reactive({
  currentPage: 1,
  pageSize: 10,
  total: 2
})

// 加载状态
const loading = ref(false)

// 内部弹窗控制
const innerDialogVisible = ref(false)
const dialogTitle = ref('新增报表权限')
const formRef = ref(null)
const treeRef = ref(null)

// 表单数据
const formData = reactive({
  id: '',
  activityType: '',
  activityId: '',
  permissions: [],
  type:[]
})

// 表单验证规则
const rules = {
  activityType: [{ required: true, message: '请选择活动类型', trigger: 'change' }],
  activityId: [{ required: true, message: '请选择活动名称', trigger: 'change' }],
  permissions: [{ required: true, message: '请选择权限报表', trigger: 'change' }]
}

// 活动名称下拉选项
const activityOptions = ref([])

// 权限树数据
const permissionTree = ref([
  {
    id: '1',
    label: '报表权限',
    children: [
      { id: '1-1', label: '销售报表' },
      { id: '1-2', label: '库存报表' },
      { id: '1-3', label: '财务报表' }
    ]
  },
  {
    id: '2',
    label: '操作权限',
    children: [
      { id: '2-1', label: '导出数据' },
      { id: '2-2', label: '打印报表' }
    ]
  }
])

const treeProps = {
  children: 'children',
  label: 'label'
}

// 获取活动类型标签
const getActivityTypeLabel = (value) => {
  const item = activityTypeOptions.find(item => item.value === value)
  return item ? item.label : value
}

// 搜索
const handleSearch = () => {
  pagination.currentPage = 1
  fetchTableData()
}

// 重置搜索
const resetSearch = () => {
  searchForm.activityType = ''
  searchForm.activityId = ''
  handleSearch()
}

// 获取表格数据
const fetchTableData = () => {
  loading.value = true
  // 这里应该是API调用
  setTimeout(() => {
    loading.value = false
  }, 500)
}

// 分页变化
const handleSizeChange = (val) => {
  pagination.pageSize = val
  fetchTableData()
}

const handleCurrentChange = (val) => {
  pagination.currentPage = val
  fetchTableData()
}

// 显示新增弹窗
const showAddDialog = () => {
  dialogTitle.value = '新增报表权限'
  resetForm()
  innerDialogVisible.value = true
}

// 编辑
const handleEdit = (row) => {
  dialogTitle.value = '编辑报表权限'
  resetForm()
  
  // 模拟从API获取详情数据
  Object.assign(formData, {
    id: row.id,
    activityType: row.activityType,
    activityId: row.activityId || '101' // 模拟数据
  })
  
  // 加载活动名称选项
  loadActivityOptions(row.activityType)
  
  // // 模拟设置选中的权限
  // nextTick(() => {
  //   treeRef.value?.setCheckedKeys(['1-1', '2-1']) // 模拟选中的权限
  // })
  
  innerDialogVisible.value = true
}

// 删除
const handleDelete = (row) => {
  ElMessageBox.confirm('确定要删除这条权限记录吗?', '提示', {
    confirmButtonText: '确定',
    cancelButtonText: '取消',
    type: 'warning'
  }).then(() => {
    // 这里应该是API调用
    ElMessage.success('删除成功')
    fetchTableData()
  }).catch(() => {
    ElMessage.info('已取消删除')
  })
}

// 重置表单
const resetForm = () => {
  formData.id = ''
  formData.activityType = ''
  formData.activityId = ''
  formData.permissions = []
  
  // nextTick(() => {
  //   treeRef.value?.setCheckedKeys([])
  //   formRef.value?.resetFields()
  // })
}

// 活动类型变化时加载活动名称
const handleActivityTypeChange = (val) => {
  formData.activityId = ''
  loadActivityOptions(val)
}

// 加载活动名称选项
const loadActivityOptions = (type) => {
  // 模拟API调用
  setTimeout(() => {
    if (type === '1') {
      activityOptions.value = [
        { id: '101', name: '双十一促销活动' },
        { id: '102', name: '618大促活动' }
      ]
    } else if (type === '2') {
      activityOptions.value = [
        { id: '201', name: '年度客户答谢会' },
        { id: '202', name: '产品发布会' }
      ]
    } else {
      activityOptions.value = []
    }
  }, 300)
}

// 提交表单
const submitForm = () => {
  formRef.value.validate(async (valid) => {
    if (valid) {
      // 获取选中的权限
      const checkedKeys = treeRef.value.getCheckedKeys()
      const halfCheckedKeys = treeRef.value.getHalfCheckedKeys()
      const allCheckedKeys = [...checkedKeys, ...halfCheckedKeys]
      
      if (allCheckedKeys.length === 0) {
        ElMessage.warning('请至少选择一个权限')
        return
      }
      
      // 这里应该是API调用
      console.log('提交数据:', {
        ...formData,
        permissions: allCheckedKeys
      })
      
      ElMessage.success(dialogTitle.value + '成功')
      innerDialogVisible.value = false
      emit('success')
      fetchTableData()
    }
  })
}

// 初始化时如果有activityId，设置搜索条件
watch(() => props.activityId, (val) => {
  if (val) {
    searchForm.activityId = val
    fetchTableData()
  }
}, { immediate: true })
</script>

<style scoped>
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