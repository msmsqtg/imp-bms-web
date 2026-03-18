<template>
  <div class="invitation-create">
    <el-card shadow="never" class="rr-view-ctx-card">
      <template #header>
        <div class="card-header">
          <h2>{{ isViewMode ? '查看邀约活动' : (isEditMode ? '编辑邀约活动' : '新增邀约活动') }}</h2>
          <el-button type="primary" @click="handleBack">返回列表</el-button>
        </div>
      </template>

      <!-- 装修配置 -->
      <page-config 
        :invitation-id="invitationId" 
        :is-view-mode="isViewMode"
        @update:invitation-id="(id) => invitationId = id"
      />
    </el-card>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed, onMounted } from 'vue';
import baseService from "@/service/baseService";
import { ElMessage } from 'element-plus';
import PageConfig from './components/page-config.vue';

export default defineComponent({
  name: "InvitationCreate",
  components: {
    PageConfig
  },
  data() {
    return {
      invitationId: ''
    };
  },
  computed: {
    isViewMode() {
      return this.$route.query.mode === 'view';
    },
    isEditMode() {
      return !!this.$route.query.id;
    }
  },
  onMounted() {
    if (this.isEditMode) {
      this.invitationId = this.$route.query.id as string;
    }
  },
  methods: {
    handleBack() {
      this.$router.push('/invitation-management/index');
    }
  }
});
</script>

<style scoped>
.invitation-create {
  padding: 20px;
  height: calc(100vh - 80px);
  overflow: hidden;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}
</style>