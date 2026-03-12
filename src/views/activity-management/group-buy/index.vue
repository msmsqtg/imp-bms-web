<template>
  <div class="group-buy-management">
    <el-card shadow="never" class="rr-view-ctx-card">
      <!-- 搜索和筛选区域 -->
      <div class="search-filter">
        <el-form :inline="true" class="demo-form-inline">
          <el-form-item label="活动标题">
            <el-input v-model="searchForm.title" placeholder="请输入活动标题"></el-input>
          </el-form-item>
          <el-form-item label="活动时间">
            <el-date-picker
              v-model="searchForm.startTime"
              type="datetime"
              placeholder="选择开始时间"
              format="YYYY-MM-DD HH:mm:ss"
              value-format="YYYY-MM-DD HH:mm:ss"
            ></el-date-picker>
          </el-form-item>
          <el-form-item label="至">
            <el-date-picker
              v-model="searchForm.endTime"
              type="datetime"
              placeholder="选择结束时间"
              format="YYYY-MM-DD HH:mm:ss"
              value-format="YYYY-MM-DD HH:mm:ss"
            ></el-date-picker>
          </el-form-item>
          <el-form-item label="活动状态">
            <el-select v-model="searchForm.status" placeholder="全部">
              <el-option label="全部" value=""></el-option>
              <el-option label="未开始" value="1"></el-option>
              <el-option label="进行中" value="2"></el-option>
              <el-option label="非暂停" value="3"></el-option>
              <el-option label="已结束" value="4"></el-option>
              <el-option label="已暂停" value="5"></el-option>
              <el-option label="已删除" value="6"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" icon="el-icon-search" @click="handleSearch">搜索</el-button>
            <el-button @click="handleReset">重置</el-button>
          </el-form-item>
        </el-form>
        <el-button type="primary" @click="handleCreate">新建活动</el-button>
      </div>

      <!-- 数据表格 -->
      <el-table :data="tableData" style="width: 100%">
        <el-table-column prop="index" label="序号" width="80"></el-table-column>
        <el-table-column prop="activityTitle" label="活动标题"></el-table-column>
        <el-table-column prop="activityName" label="活动名称"></el-table-column>
        <el-table-column prop="activityCode" label="活动编号"></el-table-column>
        <el-table-column prop="startTime" label="活动开始时间"></el-table-column>
        <el-table-column prop="endTime" label="活动结束时间"></el-table-column>
        <el-table-column prop="status" label="状态">
          <template #default="scope">
            <span v-if="scope.row.status === '已结束'" class="status-ended">{{ scope.row.status }}</span>
            <span v-else-if="scope.row.status === '已暂停'" class="status-paused">{{ scope.row.status }}</span>
            <span v-else-if="scope.row.status === '已删除'" class="status-deleted">{{ scope.row.status }}</span>
            <span v-else class="status-normal">{{ scope.row.status }}</span>
          </template>
        </el-table-column>
        <el-table-column prop="activityLink" label="活动链接">
          <template #default="scope">
            <a :href="scope.row.activityLink" target="_blank">{{ scope.row.activityLink }}</a>
          </template>
        </el-table-column>
        <el-table-column label="操作" width="300">
          <template #default="scope">
            <el-button type="text" size="small" @click="handleView(scope.row)">查看</el-button>
            <el-button type="text" size="small" @click="handleEdit(scope.row)" v-if="scope.row.status !== '已删除'">编辑</el-button>
            <el-button type="text" size="small" @click="handlePageConfig(scope.row)">页面配置</el-button>
            <el-button type="text" size="small" @click="handleGroupData(scope.row)">拼团数据</el-button>
            <el-button type="text" size="small" @click="handleEnable(scope.row)" v-if="scope.row.status === '已暂停'">开启</el-button>
            <el-button type="text" size="small" @click="handleDelete(scope.row)" v-if="scope.row.status !== '已删除'" style="color: #F56C6C;">删除</el-button>
          </template>
        </el-table-column>
      </el-table>

      <!-- 分页 -->
      <div class="pagination">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="pagination.currentPage"
          :page-sizes="[10, 20, 50, 100]"
          :page-size="pagination.pageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="pagination.total"
        ></el-pagination>
      </div>
    </el-card>
  </div>
</template>

<script lang="ts">
import baseService from "@/service/baseService";

export default {
  name: "GroupBuyManagement",
  data() {
    return {
      searchForm: {
        title: '',
        startTime: '',
        endTime: '',
        status: ''
      },
      tableData: [],
      pagination: {
        currentPage: 1,
        pageSize: 10,
        total: 0
      }
    };
  },
  created() {
    // 初始化加载数据
    this.loadData();
  },
  methods: {
    loadData() {
      // 构建请求参数
      const params = {
        pageIndex: this.pagination.currentPage,
        pageSize: this.pagination.pageSize,
        type: 2,
        title: this.searchForm.title,
        startTime: this.searchForm.startTime,
        endTime: this.searchForm.endTime
      };
      
      // 只有当 searchForm.status 不为空时，才添加 status 参数
      if (this.searchForm.status !== '') {
        params.status = this.searchForm.status;
      }

      // 构建请求头
      const headers = {
        createUserId: 4440
      };

      // 发送请求
      baseService.get('http://10.10.10.156:9002/imp-bms/team/up/list', params, headers)
        .then(res => {
          if (res.code === '00000') {
            // 处理返回数据
            this.pagination.total = res.total;
            this.tableData = res.data.map((item, index) => ({
              index: (this.pagination.currentPage - 1) * this.pagination.pageSize + index + 1,
              activityTitle: item.title,
              activityName: item.name,
              activityCode: item.no,
              startTime: item.startTime,
              endTime: item.endTime,
              status: this.getStatusText(item.status),
              activityLink: item.impUrl,
              id: item.id
            }));
          } else {
            console.error('获取活动列表失败:', res.msg);
          }
        })
        .catch(error => {
          console.error('获取活动列表失败:', error);
        });
    },
    getStatusText(status) {
      // 将数字状态转换为文字状态
      const statusMap = {
        1: '未开始',
        2: '进行中',
        3: '非暂停',
        4: '已结束',
        5: '已暂停',
        6: '已删除'
      };
      return statusMap[status] || status;
    },
    handleSearch() {
      // 搜索逻辑
      this.pagination.currentPage = 1;
      this.loadData();
    },
    handleReset() {
      // 重置逻辑
      this.searchForm = {
        title: '',
        startTime: '',
        endTime: '',
        status: ''
      };
      this.pagination.currentPage = 1;
      this.loadData();
    },
    handleCreate() {
      // 跳转到新建活动页面
      this.$router.push('/activity-management/group-buy/create');
    },
    handleView(row) {
      // 查看活动逻辑
      console.log('查看活动', row);
    },
    handleEdit(row) {
      // 编辑活动逻辑
      console.log('编辑活动', row);
    },
    handlePageConfig(row) {
      // 页面配置逻辑
      console.log('页面配置', row);
    },
    handleGroupData(row) {
      // 拼团数据逻辑
      console.log('拼团数据', row);
    },
    handleEnable(row) {
      // 开启活动逻辑
      console.log('开启活动', row);
    },
    handleDelete(row) {
      // 删除活动逻辑
      console.log('删除活动', row);
    },
    handleSizeChange(size) {
      this.pagination.pageSize = size;
      this.loadData();
    },
    handleCurrentChange(current) {
      this.pagination.currentPage = current;
      this.loadData();
    }
  }
};
</script>

<style scoped>
.group-buy-management {
  padding: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.search-filter {
  margin-bottom: 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.demo-form-inline {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
}

.demo-form-inline .el-form-item {
  margin-right: 10px;
  margin-bottom: 10px;
}

.pagination {
  margin-top: 20px;
  display: flex;
  justify-content: flex-end;
}

.status-ended {
  color: #909399;
}

.status-paused {
  color: #E6A23C;
}

.status-deleted {
  color: #F56C6C;
}

.status-normal {
  color: #67C23A;
}
</style>
