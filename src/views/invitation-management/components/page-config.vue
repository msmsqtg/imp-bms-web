<template>
  <div class="page-config">
    <!-- 左侧 Tab 导航 -->
    <div class="config-tabs">
      <el-menu
        :default-active="activeTab"
        class="el-menu-vertical-demo"
        @select="handleTabChange"
      >
        <el-menu-item index="basic">
          <span>基础设置</span>
        </el-menu-item>
        <el-menu-item index="home">
          <span>首页装修</span>
        </el-menu-item>
        <el-menu-item index="login">
          <span>登录页面</span>
        </el-menu-item>
        <el-menu-item index="data">
          <span>资料提交</span>
        </el-menu-item>
        <el-menu-item index="customer">
          <span>我的客户</span>
        </el-menu-item>
        <el-menu-item index="sign-in">
          <span>签到页面</span>
        </el-menu-item>
        <el-menu-item index="share">
          <span>分享设置</span>
        </el-menu-item>
      </el-menu>
    </div>

    <!-- 中间预览区域 -->
    <div class="preview-area" v-if="activeTab !== 'basic'">
      <div class="preview-container">
        <div class="preview-content">
          <!-- 预览内容 -->
          <div v-if="activeTab === 'home'">
            <h3>首页装修预览</h3>
            <div v-if="uiConfig.home_bg_pic" class="preview-image">
              <img :src="uiConfig.home_bg_pic" alt="首页背景图">
            </div>
            <div v-else class="preview-placeholder">
              <span>首页背景图预览</span>
            </div>
          </div>
          <div v-else-if="activeTab === 'login'">
            <h3>登录页面预览</h3>
            <div class="preview-form">
              <p>登录页面设置预览</p>
            </div>
          </div>
          <div v-else-if="activeTab === 'data'">
            <h3>资料提交预览</h3>
            <div class="preview-form">
              <p>资料提交页面设置预览</p>
            </div>
          </div>
          <div v-else-if="activeTab === 'customer'">
            <h3>我的客户预览</h3>
            <div class="preview-customer">
              <p>我的客户页面设置预览</p>
            </div>
          </div>
          <div v-else-if="activeTab === 'sign-in'">
            <h3>签到页面预览</h3>
            <div class="preview-sign-in">
              <p>签到页面设置预览</p>
            </div>
          </div>
          <div v-else-if="activeTab === 'share'">
            <h3>分享设置预览</h3>
            <div v-if="uiConfig.share_poster_pic" class="preview-image">
              <img :src="uiConfig.share_poster_pic" alt="分享海报图">
            </div>
            <div v-else class="preview-placeholder">
              <span>分享海报图预览</span>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- 基础设置专用区域 -->
    <div class="basic-setting-area" v-else>
      <div class="basic-content">
        <div class="basic-tip">
          <el-alert
            title="基础设置"
            type="info"
            :closable="false"
            show-icon
          >
            <template #default>
              <p>请填写活动的基本信息，包括活动名称、活动编码、活动类型等。</p>
              <p>填写完成后点击保存按钮，然后可以进行其他页面的装修配置。</p>
            </template>
          </el-alert>
        </div>
      </div>
    </div>

    <!-- 右侧配置区域 -->
    <div class="config-area">
      <div class="config-content">
        <!-- 基础设置 -->
        <basic-config 
          v-if="activeTab === 'basic'" 
          :form="form" 
          :is-view-mode="isViewMode"
          @update:form="(value) => form = value"
          @save-success="handleSaveSuccess"
        />

        <!-- 首页装修 -->
        <home-config 
          v-if="activeTab === 'home'" 
          :ui-config="uiConfig" 
          :invitation-id="form.id"
          :is-view-mode="isViewMode"
          @update:ui-config="(value) => uiConfig = value"
        />

        <!-- 登录页面 -->
        <login-config 
          v-if="activeTab === 'login'" 
          :form="form" 
          :ui-config="uiConfig" 
          :invitation-id="form.id"
          :is-view-mode="isViewMode"
          @update:form="(value) => form = value"
          @update:ui-config="(value) => uiConfig = value"
        />

        <!-- 资料提交 -->
        <data-config 
          v-if="activeTab === 'data'" 
          :form="form" 
          :ui-config="uiConfig" 
          :invitation-id="form.id"
          :is-view-mode="isViewMode"
          @update:form="(value) => form = value"
          @update:ui-config="(value) => uiConfig = value"
        />

        <!-- 我的客户 -->
        <customer-config 
          v-if="activeTab === 'customer'" 
          :ui-config="uiConfig" 
          :invitation-id="form.id"
          :is-view-mode="isViewMode"
          @update:ui-config="(value) => uiConfig = value"
        />

        <!-- 签到页面 -->
        <sign-in-config 
          v-if="activeTab === 'sign-in'" 
          :ui-config="uiConfig" 
          :invitation-id="form.id"
          :is-view-mode="isViewMode"
          @update:ui-config="(value) => uiConfig = value"
        />

        <!-- 分享设置 -->
        <share-config 
          v-if="activeTab === 'share'" 
          :ui-config="uiConfig" 
          :invitation-id="form.id"
          :is-view-mode="isViewMode"
          @update:ui-config="(value) => uiConfig = value"
        />
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed, onMounted } from 'vue';
import baseService from "@/service/baseService";
import { ElMessage } from 'element-plus';
import BasicConfig from './page-config/basic-config.vue';
import HomeConfig from './page-config/home-config.vue';
import LoginConfig from './page-config/login-config.vue';
import DataConfig from './page-config/data-config.vue';
import CustomerConfig from './page-config/customer-config.vue';
import SignInConfig from './page-config/sign-in-config.vue';
import ShareConfig from './page-config/share-config.vue';

export default defineComponent({
  components: {
    BasicConfig,
    HomeConfig,
    LoginConfig,
    DataConfig,
    CustomerConfig,
    SignInConfig,
    ShareConfig
  },
  name: "InvitationPageConfig",
  props: {
    invitationId: {
      type: String,
      default: ''
    },
    isViewMode: {
      type: Boolean,
      default: false
    }
  },
  emits: ['update:invitationId'],
  data() {
    return {
      activeTab: 'basic',
      form: {
        id: '',
        store_id: 0,
        name: '',
        activity_no: '',
        activity_type: '',
        activity_id: '',
        start_time: '',
        end_time: '',
        carrier_type: 3,
        login_switch: 1,
        login_whitelist_switch: 2,
        data_switch: 2,
        c_data_switch: 2,
        customer_switch: 2,
        sign_in_status: 0,
        login_form: '',
        data_form: '',
        c_data_form: '',
        share_mode: 1,
        is_del: 0,
        tenant_id: 1
      },
      uiConfig: {
        id: '',
        store_id: 0,
        invitation_id: '',
        theme_bg_pic: '',
        home_bg_pic: '',
        module_bg_color: '',
        text_color: '',
        share_poster_pic: '',
        share_bg_setting: '',
        poster_pic_text: '',
        share_text: '',
        login_setting: '',
        data_setting: '',
        c_data_setting: '',
        customer_setting: '',
        sign_in_setting: '',
        invitation_rule_setting: '',
        third_link_address: ''
      },
      isLoading: false
    };
  },
  watch: {
    invitationId: {
      handler(newId) {
        if (newId) {
          this.loadInvitationData(newId);
        }
      },
      immediate: true
    }
  },
  methods: {
    handleTabChange(tab: string) {
      this.activeTab = tab;
    },

    getActivityTypeText(type: number | string) {
      const typeMap: Record<number, string> = {
        1: '邀约',
        2: '积赞'
      };
      return typeMap[Number(type)] || type;
    },

    getCarrierTypeText(type: number | string) {
      const typeMap: Record<number, string> = {
        1: 'APP',
        2: '小程序',
        3: '公众号'
      };
      return typeMap[Number(type)] || type;
    },

    async loadInvitationData(id: string) {
      this.isLoading = true;
      try {
        // 加载基础活动数据
        const baseUrl = `${import.meta.env.VITE_APP_API}/invitation/detail`;
        const baseRes: any = await baseService.get(baseUrl, { id });
        if (baseRes.code === '00000') {
          this.form = baseRes.data;
        }

        // 加载UI配置数据
        const uiUrl = `${import.meta.env.VITE_APP_API}/invitation/ui/detail`;
        const uiRes: any = await baseService.get(uiUrl, { invitation_id: id });
        if (uiRes.code === '00000') {
          this.uiConfig = uiRes.data;
        }
      } catch (error) {
        console.error('加载数据失败:', error);
        ElMessage.error('加载数据失败，请重试');
      } finally {
        this.isLoading = false;
      }
    },

    handleSaveSuccess() {
      // 基础设置保存成功后的处理
      console.log('基础设置保存成功');
    }
  }
});
</script>

<style scoped>
.page-config {
  display: flex;
  height: calc(100vh - 120px);
  overflow: hidden;
}

.config-tabs {
  width: 180px;
  border-right: 1px solid #e4e7ed;
  background-color: #fff;
}

:deep(.el-menu-vertical-demo) {
  background-color: #fff;
}

:deep(.el-menu-item) {
  background-color: #f5f7fa;
  margin: 5px 10px;
  border-radius: 4px;
  border: 1px solid #e4e7ed;
  color: #000;
  font-weight: 500;
  padding: 12px 20px;
}

:deep(.el-menu-vertical-demo .el-menu-item:not(.is-active)) {
  color: #000;
  background-color: #f5f7fa;
}

:deep(.el-menu-vertical-demo .el-menu-item:not(.is-active):hover) {
  color: #409eff;
  background-color: #ecf5ff;
}

:deep(.el-menu-item.is-active) {
  color: #fff;
  background-color: #409eff;
  border-color: #409eff;
  font-weight: 600;
}

:deep(.el-menu-item:hover) {
  color: #409eff;
  background-color: #ecf5ff;
  border-color: #c6e2ff;
  font-weight: 500;
}

.preview-area {
  flex: 1;
  padding: 20px;
  overflow-y: auto;
  display: flex;
  justify-content: center;
  align-items: flex-start;
}

.preview-container {
  width: 375px;
  height: 667px;
  border: 1px solid #e4e7ed;
  border-radius: 8px;
  overflow: hidden;
  background-color: #fff;
  position: relative;
}

.basic-setting-area {
  flex: 1;
  padding: 20px;
  overflow-y: auto;
  display: flex;
  justify-content: center;
  align-items: flex-start;
}

.basic-content {
  width: 100%;
  max-width: 600px;
}

.basic-tip {
  margin-bottom: 20px;
}

.preview-content {
  padding: 20px;
  height: 100%;
  overflow-y: auto;
}

.preview-image img {
  max-width: 100%;
  max-height: 300px;
  object-fit: cover;
  border-radius: 4px;
}

.preview-placeholder {
  width: 100%;
  height: 200px;
  border: 1px dashed #e4e7ed;
  border-radius: 4px;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #f9f9f9;
  color: #909399;
}

.config-area {
  width: 400px;
  border-left: 1px solid #e4e7ed;
  background-color: #f5f7fa;
  overflow-y: auto;
}

.config-content {
  padding: 20px;
}

.config-panel {
  background-color: #fff;
  padding: 20px;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.upload-container {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  flex-wrap: wrap;
}

.upload-row {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}

.upload-placeholder {
  width: 150px;
  height: 150px;
  border: 1px solid #e4e7ed;
  border-radius: 4px;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #f9f9f9;
  margin-right: 10px;
}

.placeholder-icon {
  font-size: 48px;
  color: #409eff;
  font-weight: bold;
}

.upload-button {
  margin-right: 10px;
}

.upload-tip {
  color: #909399;
  font-size: 12px;
}

.image-preview {
  margin-right: 10px;
}

.preview-image {
  width: 150px;
  height: 150px;
  object-fit: cover;
  border: 1px solid #e4e7ed;
  border-radius: 4px;
}

/* 响应式调整 */
@media (max-width: 1200px) {
  .config-area {
    width: 350px;
  }
}

@media (max-width: 1024px) {
  .page-config {
    flex-direction: column;
  }
  
  .config-tabs {
    width: 100%;
    height: 60px;
    border-right: none;
    border-bottom: 1px solid #e4e7ed;
  }
  
  .preview-area {
    height: 400px;
  }
  
  .config-area {
    width: 100%;
    border-left: none;
    border-top: 1px solid #e4e7ed;
    flex: 1;
  }
}
</style>