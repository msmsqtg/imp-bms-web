<template>
  <div class="invitation-create">
    <el-card shadow="never" class="rr-view-ctx-card">
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
import emits from "@/utils/emits";
import { EMitt } from "@/constants/enum";
import { useAppStore } from "@/store";

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
  watch: {
    // 监听路由变化
    $route: {
      handler(route) {
        console.log('=== 路由变化 ===');
        console.log('route.query:', route.query);
        if (route.query.id) {
          this.invitationId = route.query.id as string;
          console.log('通过路由变化设置 invitationId:', this.invitationId);
        }
        
        // 延迟更新标签页标题，确保在路由拦截器之后执行
        setTimeout(() => {
          this.updateTabTitle();
        }, 100);
      },
      immediate: true
    }
  },
  onMounted() {
    console.log('=== create.vue onMounted ===');
    console.log('$route.query:', this.$route.query);
    console.log('isEditMode:', this.isEditMode);
    console.log('isViewMode:', this.isViewMode);
    // 直接从路由参数中获取 id，不管是编辑还是查看模式
    if (this.$route.query.id) {
      this.invitationId = this.$route.query.id as string;
      console.log('设置 invitationId:', this.invitationId);
    }
    
    // 延迟更新标签页标题，确保在路由拦截器之后执行
    setTimeout(() => {
      this.updateTabTitle();
    }, 100);
  },
  methods: {
    updateTabTitle() {
      const store = useAppStore();
      let title = '新增邀约活动';
      if (this.isViewMode) {
        title = '查看邀约活动';
      } else if (this.isEditMode) {
        title = '编辑邀约活动';
      }
      
      console.log('=== 更新标签页标题 ===');
      console.log('当前路由:', this.$route.fullPath);
      console.log('新标题:', title);
      
      // 直接修改store中的tabs状态
      const tabs = [...store.state.tabs];
      const currentTabIndex = tabs.findIndex(tab => tab.value === this.$route.fullPath);
      
      if (currentTabIndex !== -1) {
        // 更新现有标签页的标题
        tabs[currentTabIndex].label = title;
        store.updateState({ tabs });
        console.log('更新现有标签页标题成功');
      } else {
        // 添加新的标签页
        emits.emit(EMitt.OnPushMenuToTabs, {
          label: title,
          value: this.$route.fullPath,
          mete: {}
        });
        console.log('添加新标签页成功');
      }
    },
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
