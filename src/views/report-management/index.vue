<template>
  <div class="report-container">
    <!-- 报表选择区域 -->
    <el-card class="report-selector">
      <el-form :inline="true" :model="reportForm">
        <el-form-item label="选择活动">
          <el-select
            v-model="reportForm.roleImpId"
            placeholder="请选择活动"
            clearable
            filterable
            style="width: 200px"
             @change="handleImpIdChange"
            :filter-method="filterActivity"
          >
            <el-option
              v-for="item in filteredActivities"
              :key="item.roleImpId"
              :label="item.impName"
              :value="item.roleImpId"
            />
          </el-select>
        </el-form-item>
        
        <el-form-item label="选择报表">
          <el-select
            v-model="reportForm.reportId"
            placeholder="请选择报表"
            clearable
            style="width: 200px"
            @change="handleReportChange"
          >
            <el-option
              v-for="item in reportTypeOptions"
              :key="item.reportId"
              :label="item.reportName"
              :value="item.reportId"
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
        <el-form-item label="机构" key="orgIds">
          <!-- <el-select
            v-model="searchForm.orgIds"
            placeholder="请选择机构"
            clearable
            filterable
            multiple
            collapse-tags
            :filter-method="filterOrg"
            style="width: 150px"
          >
            <el-option
              v-for="item in filteredOrgs"
              :key="item.orgId"
              :label="item.orgName"
              :value="item.orgId"
            />
          </el-select> -->
          <el-cascader
            v-model="searchForm.orgIds"          
            :options="orgOptions"
            :props="props"
            collapse-tags
            clearable></el-cascader>
        </el-form-item>
        
        <el-form-item label="用户手机号" key="phone">
          <el-input
            v-model="searchForm.phone"
            placeholder="请输入手机号"
            clearable
            style="width: 180px"
          />
        </el-form-item>
        
        <el-form-item label="代理人工号" key="agentCode">
          <el-input
            v-model="searchForm.agentCode"
            placeholder="请输入工号"
            clearable
            style="width: 150px"
          />
        </el-form-item>
         <el-form-item label="核销状态" v-if="reportForm.reportId==3" key="status">
          <el-select
            v-model="searchForm.status"
            placeholder="请选择状态"
            clearable
            style="width: 120px"
          >          
            <el-option label="全部" :value="0"></el-option>
            <el-option label="待核销" :value="1"></el-option>
            <el-option label="无需核销" :value="3"></el-option>
            <el-option label="已核销" :value="2"></el-option>
          </el-select>
        </el-form-item>
         <el-form-item label="状态" v-if="reportForm.reportId==2" key="status">
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
         <el-form-item label="拼团用户" v-if="reportForm.reportId==2" key="impType">
          <el-select
            v-model="searchForm.impType"
            placeholder="请选择状态"
            clearable
            style="width: 120px"
          >          
            <el-option label="全部" :value="0"></el-option>
            <el-option label="团长" :value="1"></el-option>
            <el-option label="团员" :value="2"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="组团编号" key="orderNo">
          <el-input
            v-model="searchForm.orderNo"
            placeholder="请输入组团编号"
            clearable
            style="width: 150px"
          />
        </el-form-item>
        <el-form-item label="奖品名称" v-if="reportForm.reportId==3" prop="productName" key="productName">
          <el-input v-model.trim="searchForm.productName" placeholder="请输入奖品名称"></el-input>
        </el-form-item>
        <el-form-item label="订单时间">         
          <el-date-picker
            v-model="dateRange"
            type="datetimerange"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            value-format="YYYY-MM-DD HH:mm:ss" 
            :default-time="defaultTime"          
        ></el-date-picker>
        </el-form-item>        
        <el-form-item>
          <el-button type="primary" @click="handleSearch">搜索</el-button>
          <el-button @click="resetSearch">重置</el-button>
          <el-button type="success" @click="handleExport">导出</el-button>
          <!-- <el-button type="info" @click="showExportRecords">导出记录</el-button> -->
        </el-form-item>
      </el-form>
    </el-card>
    <el-card class="table-box" v-if="reportForm.reportId">
    <!-- 表格区域 -->
    <template v-if="reportForm.reportId === 1">    
      <el-table
        :data="tableData"
        border
        style="width: 100%"
        v-loading="loading"
        key="table1"
      >
         <el-table-column prop="orderNo" label="拼团编号"></el-table-column>
         <el-table-column prop="userPhone" label="用户手机号">
          <template  #default="{ row }">           
            <div v-if="row.userPhone || row.userHelpPhone">{{row.productImpType === "1"?row.userPhone:row.userHelpPhone}}</div>
          </template>
        </el-table-column>
        <!--      开团信息-->
        <el-table-column prop="teamLeader" label="开团信息" width="200">
          <template  #default="{ row }">
            <div v-if="row.userNickname">姓名：{{row.userNickname}}</div>
            <div v-if="row.userPhone">手机号：{{row.userPhone}}</div>
          </template>
        </el-table-column>
        <el-table-column prop="userPhone" label="代理人" width="200">
          <template  #default="{ row }">
            <div v-if="row.agent">
              <div v-if="row.agent.agentCode">工号：{{row.agent.agentCode}}</div>
              <div v-if="row.agent.agentName">姓名：{{row.agent.agentName}}</div>
              <div v-if="row.agent.agentPhone">手机号：{{row.agent.agentPhone}}</div>
              <div v-if="row.agent.agentOrg">机构：{{row.agent.agentOrg}}</div>
            </div>
          </template>
        </el-table-column>
        <el-table-column prop="userNickname" label="用户昵称"></el-table-column>

        <el-table-column prop="status" label="状态">
          <template  #default="{ row }">
            {{row.status==1?'进行中':row.status==2?'拼团失败 ':'拼团成功'}}
          </template>
        </el-table-column>

        <el-table-column prop="status" label="奖品类型">
          <template  #default="{ row }">
            {{row.productImpType === "1" ? '开团' : '助力'}}
          </template>
        </el-table-column>

        <el-table-column prop="productName" label="奖品名称">
        </el-table-column>

        <el-table-column prop="createTime" label="订单时间"></el-table-column>
        <el-table-column prop="writeOffStatus" label="核销状态">
          <template  #default="{ row }">
            {{row.writeOffStatus == "1" ? '待核销' : row.writeOffStatus == "2" ? '已核销' : '无需核销'}}
          </template>
        </el-table-column>
        <el-table-column prop="writeOffTime" label="核销时间"></el-table-column>
        <el-table-column prop="writeOffPhone" label="核销人手机号"></el-table-column>
        <el-table-column fixed="right" label="操作" width="100">
          <template  #default="{ row }">
            <el-button @click="handleClick(row)" type="text" >拼团记录</el-button>
          </template>
        </el-table-column>
      </el-table>
    </template>        
    <template v-else-if="reportForm.reportId === 2">
      <el-table
          :data="tableData"
          border
          style="width: 100%"
          v-loading="loading"
          key="table2"
        >
        <el-table-column prop="orderNo" label="拼团编号" width="170px"></el-table-column>
         <el-table-column prop="userPhone" label="用户手机号">
          <template  #default="{ row }">           
            <div v-if="row.userPhone || row.userHelpPhone">{{row.productImpType === "1"?row.userPhone:row.userHelpPhone}}</div>
          </template>
        </el-table-column>
        <!--      开团信息-->
        <el-table-column prop="teamLeader" label="开团信息" width="200">
          <template  #default="{ row }">
            <div v-if="row.userNickname">姓名：{{row.userNickname}}</div>
            <div v-if="row.userPhone">手机号：{{row.userPhone}}</div>
          </template>
        </el-table-column>
        <!-- 助力信息 -->
        <el-table-column prop="teamMember" label="助力信息">
          <template #default="{ row }">
            <div v-if="row.userHelpName">姓名：{{row.userHelpName}}</div>
          </template>
        </el-table-column>
        <el-table-column prop="userPhone" label="代理人" width="200">
          <template #default="{ row }">
            <div v-if="row.agent">
              <div v-if="row.agent.agentCode">工号：{{row.agent.agentCode}}</div>
              <div v-if="row.agent.agentName">姓名：{{row.agent.agentName}}</div>
              <div v-if="row.agent.agentPhone">手机号：{{row.agent.agentPhone}}</div>
              <div v-if="row.agent.agentOrg">机构：{{row.agent.agentOrg}}</div>
            </div>
          </template>
        </el-table-column>
        <el-table-column prop="productName" label="拼团奖品名称"></el-table-column>
        <el-table-column prop="price" label="拼团商品原价">
          <template  #default="{ row }">
            <div v-if="row.price"> {{(row.price / 100).toFixed(2)}}元</div>
          </template>
        </el-table-column>
        <el-table-column prop="teamPrice" label="拼团商品成团价">
          <template  #default="{ row }">
            <div v-if="row.teamPrice"> {{(row.teamPrice / 100).toFixed(2)}}元</div>
          </template>
        </el-table-column>

        <el-table-column prop="status" label="状态">
          <template  #default="{ row }">
            {{row.status==1?'进行中':row.status==2?'拼团失败 ':'拼团成功'}}
          </template>
        </el-table-column>
        <el-table-column prop="createTime" label="订单时间"></el-table-column>
        <el-table-column prop="writeOffStatus" label="核销状态">
          <template  #default="{ row }">
            {{row.writeOffStatus == "1" ? '待核销' : row.writeOffStatus == "2" ? '已核销' : '无需核销'}}
          </template>
        </el-table-column>
        <el-table-column prop="writeOffTime" label="核销时间"></el-table-column>
        <el-table-column prop="writeOffPhone" label="核销人手机号"></el-table-column>
        <el-table-column fixed="right" prop="createTime" label="说明" width="100">
          <template  #default="{ row }">
            {{row.productImpType==1?'团长':'团员'}}
          </template>
          
        </el-table-column>
      </el-table>
    </template>        
    <template v-else-if="reportForm.reportId === 3">
      <el-table
        :data="tableData"
        border
        style="width: 100%"
        v-loading="loading"
          key="table3"
      >
        <el-table-column
          type="index"
          label="序号"
          width="60"
          align="center"
        />
        
        <!-- 拼团编号-->
        <el-table-column prop="orderNo" label="拼团编号" width="170px"></el-table-column>
        <!--      用户手机号-->
        <el-table-column prop="userPhone" label="用户手机号">
          <template  #default="{ row }">           
            <div v-if="row.userPhone || row.userHelpPhone">{{row.productImpType === "1"?row.userPhone:row.userHelpPhone}}</div>
          </template>
        </el-table-column>
        <!--      开团信息-->
        <el-table-column prop="teamLeader" label="开团信息" width="200">
          <template  #default="{ row }">
            <div v-if="row.userNickname">姓名：{{row.userNickname}}</div>
            <div v-if="row.userPhone">手机号：{{row.userPhone}}</div>
          </template>
        </el-table-column>
        <!--      助力信息-->
        <el-table-column prop="teamMember" label="助力信息">
          <template  #default="{ row }">
            <div v-if="row.userHelpName">姓名：{{row.userHelpName}}</div>
          </template>
        </el-table-column>
        <!--      代理人-->
        <el-table-column  label="代理人" width="200">
          <template  #default="{ row }">
            <div v-if="row.agent.agentCode">工号：{{row.agent.agentCode}}</div>
            <div v-if="row.agent.agentName">姓名：{{row.agent.agentName}}</div>
            <div v-if="row.agent.agentPhone">手机号：{{row.agent.agentPhone}}</div>
            <div v-if="row.agent.agentOrg">机构：{{row.agent.agentOrg}}</div>
          </template>
        </el-table-column>
        <!--      状态-->
        <el-table-column prop="status" label="状态">
          <template  #default="{ row }">
            {{row.status === 1 ? '进行中' : row.status === 2 ? '拼团失败' : '拼团成功'}}
          </template>
        </el-table-column>
        <el-table-column prop="productImpType" label="奖品类型">
          <template  #default="{ row }">
            {{row.productImpType == "1" ? '开团' : '助力'}}
          </template>
        </el-table-column>
        <el-table-column prop="productName" label="奖品名称"></el-table-column>
        <el-table-column prop="createTime" label="订单时间"></el-table-column>
        <el-table-column prop="writeOffStatus" label="核销状态">
          <template  #default="{ row }">
            {{row.writeOffStatus == "1" ? '待核销' : row.writeOffStatus == "2" ? '已核销' : '无需核销'}}
          </template>
        </el-table-column>
        <el-table-column prop="writeOffTime" label="核销时间"></el-table-column>
        <el-table-column prop="writeOffPhone" label="核销人手机号"></el-table-column>
      </el-table>
    </template>      
    <!-- 分页 -->
    <el-pagination
      class="pagination"
      v-model:current-page="pagination.pageIndex"
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
        <el-table-column prop="reportId" label="报表类型" width="120">
          <template #default="{ row }">
            {{ getReportTypeLabel(row.reportId) }}
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
     <el-dialog title="拼团情况"  v-model="dialogVisible" >
      <el-table :data="dialogTableData" style="width: 100%" :key="num">
        <el-table-column type="index" label="序号"></el-table-column>
        <el-table-column prop="orderNo" label="拼团编号"></el-table-column>
        <el-table-column prop="userPhone" label="用户手机号"></el-table-column>
        <el-table-column prop="status" label="拼团结果">
          <template #default="{ row }">
            {{row.status==1?'进行中':row.status==2?'拼团失败 ':'拼团成功'}}
          </template>
        </el-table-column>
        <el-table-column prop="createTime" label="创建时间">
        </el-table-column>
      </el-table>
    </el-dialog>
  </div>
</template>

<script setup>
import { ref, reactive, computed,onMounted} from 'vue'
import { ElMessage,ElCascader  } from 'element-plus'
///imp/activity/leader/list?impId=32&pageIndex=1&pageSize=10
import { CacheToken } from "@/constants/cacheKey";
import baseService from "@/service/baseService";
import { useAppStore } from "@/store";
import { truncate } from 'lodash';
import { getToken } from "@/utils/cache";
import app from "@/constants/app";
import axios from "axios";
const store = useAppStore();
const props =  reactive({
  label: 'name', // 指定显示的字段为 name
  value: 'id',   // 指定绑定值的字段为 id
  children: 'children', // 子选项字段为 children
  multiple: true
})


const state = reactive({
  loading: false
});
// 报表表单
const reportForm = reactive({
  roleImpId: '',
  reportId: ''
})
const dialogVisible = ref(false)
// 报表类型选项
const reportTypeOptions = ref([])
const filteredActivities = ref([])
// 当前报表名称
const currentReportName = computed(() => {
  const item = reportTypeOptions.value.find(item => item.reportId === reportForm.reportId)
  return item ? item.reportName : '请选择报表'
})

// 活动选项
const activityOptions = ref([]);
const dialogTableData = ref([])
const num = ref(1)
// 搜索表单
const searchForm = reactive({
  orgIds:[],
  agentNames:"",
  phone: '',
  agentCode: '',
  orderNo: '',
  startTime: '',
  endTime:'',
  status: '',
  impType:0
})

// 机构选项
const orgOptions = ref([])
const filteredOrgs = ref([])
// const filterOrg = (query) => {
//   const searchText = query?.toLowerCase() || '';
//   filteredOrgs.value = orgOptions.value.filter(item =>
//     item.orgName && item.orgName.toLowerCase().includes(searchText)
//   );
// };
// 状态选项
const statusOptions = ref([
  { value: 0, label: '全部' },
  { value: 1, label: '进行中' },
  { value: 3, label: '拼团成功' },
  { value: 2, label: '拼团失败' }
])
//获取活动列表
const getActivityList = ()=>{
   baseService
    .get("/imp/activity/user/imp/list", {})
    .then((res) => {
      state.loading = false;      
      if (res.code == 200) {
       activityOptions.value = res.data || [];
       filteredActivities.value = [...res.data]
      } else {
        ElMessage.error(res.msg);
      }
      })
      .catch(() => {
        state.loading = false;     
      });
}
//活动列表数据修改
const handleImpIdChange = (e) =>{
  if(e){
    reportForm.reportId = "";
    searchForm.orgIds = []
    searchForm.agentNames = ""
     baseService
    .get("/imp/activity/user/imp/report/list", {
      roleImpId:e
    }).then((res) => {
      state.loading = false;
      console.log("resres",res)
      if (res.code == 200) {      
        reportTypeOptions.value = res.data;       
      } else {
        ElMessage.error(res.msg);
      }
      })
      .catch(() => {
        state.loading = false;     
      });
     baseService
    .get("/imp/activity/user/imp/org/list", {
      roleImpId:e
    }).then((res) => {
      state.loading = false;
      console.log("resres",res)
      if (res.code == 200) {      
       orgOptions.value = [res.data];
        // filteredOrgs.value = [...res.data]       
      } else {
        ElMessage.error(res.msg);
      }
      })
      .catch(() => {
        state.loading = false;     
      });
    //imp/activity/user/imp/org/list
  }
}
const filterActivity = (query) => {
  const searchText = query?.toLowerCase() || '';
  filteredActivities.value = activityOptions.value.filter(item => 
    item.impName && item.impName.toLowerCase().includes(searchText)
  );
};
// 表格数据
const tableData = ref([])
const loading = ref(false)
const dateRange = ref([])
const defaultTime = [
    new Date(2000, 1, 1, 0, 0, 0),
    new Date(2000, 2, 1, 23, 59, 59),
  ]
// 分页
const pagination = reactive({
  pageIndex: 1,
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
// 获取报表类型标签
const getReportTypeLabel = (type) => {
  const item = reportTypeOptions.value.find(item => item.value === type)
  return item ? item.label : type
}

// 报表类型变化
const handleReportChange = (val) => {
  if (val) {
    resetSearch()
  } else {
    tableData.value = []
  }
}
	onMounted(() => {
    getActivityList();
  })
// 搜索
const handleSearch = () => {
  pagination.pageIndex = 1
  fetchTableData()
}

// 重置搜索
const resetSearch = () => {
  searchForm.orgIds = []
  searchForm.agentNames =""
  searchForm.phone = ''
  searchForm.agentCode = ''
  searchForm.orderNo = ''
  searchForm.startTime = ""
  searchForm.endTime = ""
  searchForm.status = '';
  searchForm.impType = 0
  dateRange.value = [];
  handleSearch();
}
const findPathNames = (options, path)=> {
  const names = [];
  let currentOptions = options;
  console.log("options",options);
  for (const id of path) {
    if (!Array.isArray(currentOptions)) {
      break; // 如果当前层级不是数组，停止查找
    }

    const found = currentOptions.find(option => option.id === id);
    if (found) {
      names.push(found.name);
      currentOptions = found.children || []; // 确保 children 是数组
    } else {
      break; // 如果找不到对应的 id，停止查找
    }
  }
  return names;
}
// 获取表格数据
const fetchTableData = () => {
  if (!reportForm.reportId) {
    ElMessage.warning('请先选择报表类型')
    return
  } 
  if(dateRange.value && dateRange.value.length>0){
    searchForm.startTime = dateRange.value[0];
    searchForm.endTime = dateRange.value[1]
  }else{
    searchForm.startTime = ""
    searchForm.endTime = ""
  }
  console.log('searchForm.orgIds',searchForm.orgIds);
  if(searchForm.orgIds.length>0){
     let org = searchForm.orgIds.map(path => findPathNames(orgOptions.value, path)) // 获取每条路径的 name 数组
    .flat(); 
     searchForm.agentNames = [...new Set(org)].join(',')
  }else{
    searchForm.agentNames="";
  } 
  let impId =0;
  filteredActivities.value.map(item=>{
    if(item.roleImpId==reportForm.roleImpId) impId = item.impId
  })
  loading.value = true
  if(reportForm.reportId==1){
    //拼团记录
    baseService
    .get("/imp/activity/leader/list", {
      impId,
      roleImpId:reportForm.roleImpId,
      ...pagination,      
      ...searchForm,
      orgIds:''
    })
    .then((res) => {
      state.loading = false;
      console.log("resres",res)
      if (res.code == 200) {
        loading.value = false;
        if(res.data.data){
          tableData.value = res.data.data || [];
          pagination.total = Number(res.data.total);
        }else{
          tableData.value = []
          pagination.total = 0
        }
      } else {
        ElMessage.error(res.msg);
      }
      })
      .catch(() => {
        state.loading = false;     
      });
  }
  if(reportForm.reportId==2){
    //拼团日志
    baseService
    .get("/imp/activity/member/list", {
      impId,
      roleImpId:reportForm.roleImpId,
      ...pagination,
      ...searchForm,
      orgIds:''
    })
    .then((res) => {
      state.loading = false;
      console.log("resres",res)
      if (res.code == 200) {
        loading.value = false;
        if(res.data.data){
          tableData.value = res.data.data || [];
          pagination.total = Number(res.data.total);
        }else{
          tableData.value = []
          pagination.total = 0
        }
      } else {
        ElMessage.error(res.msg);
      }
      })
      .catch(() => {
        state.loading = false;     
      });
  }
  if(reportForm.reportId==3){
    //拼团奖品
    baseService
    .get("/imp/activity/product/list", {
      impId,
      roleImpId:reportForm.roleImpId,
      ...pagination,
      ...searchForm,
      orgIds:''
    })
    .then((res) => {
      state.loading = false;
      console.log("resres",res)
      if (res.code == 200) {
        loading.value = false;
        if(res.data.data){
          tableData.value = res.data.data || [];
          pagination.total = Number(res.data.total);
        }else{
          tableData.value = []
          pagination.total = 0
        }
      } else {
        ElMessage.error(res.msg);
      }
      })
      .catch(() => {
        state.loading = false;     
      });
  }
}

// 分页大小变化
const handleSizeChange = (val) => {
  pagination.pageSize = val
  fetchTableData()
}

// 当前页变化
const handleCurrentChange = (val) => {
  pagination.pageIndex = val
  fetchTableData()
}
const  handleClick=(row) =>{
  num.value++;
  baseService
  .get("/imp/activity/leader/detail", {
      orderId: Number(row.id), pageIndex: 1, pageSize: 100 
  })
  .then((res) => {
    state.loading = false;
    console.log("resres",res)
    if (res.code == 200) {
      dialogVisible.value = true;
      dialogTableData.value = res.data.data || []
    } else {
      ElMessage.error(res.msg);
    }
    })
    .catch(() => {
      state.loading = false;     
    });
  
}
// 导出
const handleExport = () => {
  console.log(reportForm.reportType)
  if (!reportForm.reportId) {
    ElMessage.warning('请先选择报表类型')
    return
  }
  if(dateRange.value.length>0){
    searchForm.startTime = dateRange.value[0];
    searchForm.endTime = dateRange.value[1]
  }else{
    searchForm.startTime = ""
    searchForm.endTime = ""
  }
  if(searchForm.orgIds.length>0){
     let org = orgOptions.value.filter(item => searchForm.orgIds.includes(item.orgId)).map(item => item.orgName);
     searchForm.agentNames = org.join(',')
  }else{
    searchForm.agentNames="";
  }
  let impId = 0;
  filteredActivities.value.map(item=>{
    if(item.roleImpId==reportForm.roleImpId) impId = item.impId
  }) 
  const token = getToken();
  if(reportForm.reportId==1){
    //拼团记录
    axios
    .get(app.api+"/imp/activity/leader/list", {params:{
     impId,
     roleImpId:reportForm.roleImpId,
      ...pagination,      
      ...searchForm,
      orgIds:'',
      export:true     
    }, 
    headers: {
      'Content-Type': 'application/json',
      'token': token          
    },responseType: 'blob'})
    .then((res) => {
       const content = res.data
       exportExcel(content,'拼团列表')  
      
      })
      .catch(() => {
        state.loading = false;     
      });
  }
  if(reportForm.reportId==2){
    //拼团日志
     axios
    .get(app.api+"/imp/activity/member/list", {params:{
     impId,
     roleImpId:reportForm.roleImpId,
      ...pagination,      
      ...searchForm,
      orgIds:'',
      export:true     
    }, 
    headers: {
      'Content-Type': 'application/json',
      'token': token          
    },responseType: 'blob'})
    .then((res) => {
       const content = res.data
       exportExcel(content,'拼团日志') 
      })
      .catch(() => {
        state.loading = false;     
      });
     
  }
  if(reportForm.reportId==3){
    //拼团奖品
     axios
    .get(app.api+"/imp/activity/product/list", {params:{
     impId,
      roleImpId:reportForm.roleImpId,
      ...pagination,      
      ...searchForm,
      orgIds:'',
      export:true     
    }, 
    headers: {
      'Content-Type': 'application/json',
      'token': token          
    },responseType: 'blob'})
    .then((res) => {
       const content = res.data
       exportExcel(content,'奖品列表')  
      
      })
      .catch(() => {
        state.loading = false;     
      });
  }
  // ElMessage.success('导出任务已提交，请稍后在导出记录中查看')
  
  // // 模拟添加到导出记录
  // const now = new Date()
  // const timestamp = now.toISOString().replace(/[-:]/g, '').split('.')[0]
  // exportRecords.value.unshift({
  //   id: Date.now().toString(),
  //   fileName: `${currentReportName.value}_${timestamp}.xlsx`,
  //   reportType: reportForm.reportType,
  //   createTime: now.toLocaleString(),
  //   status: 'success',
  //   filePath: `/exports/${reportForm.reportType}_${timestamp}.xlsx`
  // })
}
const exportExcel = (content,fileName) =>{
   const now = new Date()
   const timestamp = `${now.getFullYear()}${(now.getMonth()+1).toString().padStart(2, '0')}${now.getDate().toString().padStart(2, '0')}_${now.getHours().toString().padStart(2, '0')}${now.getMinutes().toString().padStart(2, '0')}${now.getSeconds().toString().padStart(2, '0')}`;
   const blob = new Blob([content], {
      type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'
    }) // 构造一个blob对象来处理数据    
    // let blob = new Blob([data], {type: 'application/vnd.ms-excel'})
    // 对于<a>标签，只有 Firefox 和 Chrome（内核） 支持 download 属性
    // IE10以上支持blob但是依然不支持download
    ElMessage.success('导出成功')
    if ('download' in document.createElement('a')) {
      // 支持a标签download的浏览器
      const link = document.createElement('a') // 创建a标签
      link.download = `${fileName}_${timestamp}.xlsx`, // a标签添加属性
      link.style.display = 'none'
      link.href = URL.createObjectURL(blob)
      document.body.appendChild(link)
      link.click() // 执行下载
      URL.revokeObjectURL(link.href) // 释放url
      document.body.removeChild(link) // 释放标签
    } else {
      // 其他浏览器
      navigator.msSaveBlob(blob, fileName)
    }
}
// 显示导出记录
const showExportRecords = () => {
   if (!reportForm.reportType) {
    ElMessage.warning('请先选择报表类型')
    return
  }
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