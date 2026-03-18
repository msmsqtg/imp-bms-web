<template>
  <div class="page-config">
    <!-- 顶部 Tab 导航 -->
    <div class="config-tabs">
      <el-menu
        :default-active="activeTab"
        class="el-menu-demo"
        mode="horizontal"
        @select="handleTabChange"
      >
        <el-menu-item index="basic">
          <span>基础设置</span>
        </el-menu-item>
        <el-menu-item index="login">
          <span>登录页面</span>
        </el-menu-item>
      </el-menu>
    </div>

    <!-- 配置区域 -->
    <div class="config-area">
      <div class="config-content">
        <!-- 基础设置 -->
        <basic-config 
          v-if="activeTab === 'basic'" 
          :form="form" 
          :is-view-mode="isViewMode"
          @update:form="(value: typeof form) => form = value"
          @save-success="handleSaveSuccess"
        />

        <!-- 登录页面 -->
        <login-config 
          v-if="activeTab === 'login'" 
          :form="form" 
          :ui-config="uiConfig" 
          :invitation-id="form.id"
          :is-view-mode="isViewMode"
          @update:form="(value: typeof form) => form = value"
          @update:ui-config="(value: typeof uiConfig) => uiConfig = value"
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
import LoginConfig from './page-config/login-config.vue';

export default defineComponent({
  components: {
    BasicConfig,
    LoginConfig
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
        console.log('=== invitationId 变化 ===');
        console.log('新的 invitationId:', newId);
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
        // 加载活动数据
        const url = `${import.meta.env.VITE_APP_API}/api/invitation/activity/${id}`;
        console.log('=== 查看/编辑活动接口调用开始 ===');
        console.log('请求URL:', url);
        
        const startTime = Date.now();
        const res: any = await baseService.get(url);
        const endTime = Date.now();
        const duration = endTime - startTime;
        
        console.log('=== 查看/编辑活动接口调用成功 ===');
        console.log('请求耗时:', duration + 'ms');
        console.log('响应数据:', JSON.stringify(res, null, 2));
        
        if (res.code === '00000') {
          const activityData = res.data.activity;
          // 转换数据格式，适配表单结构
          this.form = {
            id: activityData.id,
            store_id: activityData.storeId,
            name: activityData.name,
            activity_no: activityData.activityNo || '',
            activity_type: activityData.activityType,
            activity_id: activityData.activityId,
            start_time: activityData.startTime,
            end_time: activityData.endTime,
            carrier_type: activityData.carrierType,
            login_switch: activityData.loginSwitch,
            login_whitelist_switch: activityData.loginWhitelistSwitch,
            data_switch: activityData.dataSwitch,
            c_data_switch: activityData.cdataSwitch || 2,
            customer_switch: activityData.customerSwitch,
            sign_in_status: activityData.signInStatus,
            login_form: activityData.loginForm,
            data_form: activityData.dataForm,
            c_data_form: activityData.cdataForm,
            share_mode: activityData.shareMode,
            is_del: activityData.isDel,
            tenant_id: activityData.tenantId
          };
          
          // 转换UI配置数据
          this.uiConfig = {
            id: activityData.uiId,
            store_id: activityData.uiStoreId,
            invitation_id: activityData.invitationId,
            theme_bg_pic: activityData.themeBgPic,
            home_bg_pic: activityData.homeBgPic,
            module_bg_color: activityData.moduleBgColor,
            text_color: activityData.textColor,
            share_poster_pic: activityData.sharePosterPic,
            share_bg_setting: activityData.shareBgSetting,
            poster_pic_text: activityData.posterPicText,
            share_text: activityData.shareText,
            login_setting: activityData.loginSetting,
            data_setting: activityData.dataSetting,
            c_data_setting: activityData.cdataSetting,
            customer_setting: activityData.customerSetting,
            sign_in_setting: activityData.signInSetting,
            invitation_rule_setting: activityData.invitationRuleSetting,
            third_link_address: activityData.thirdLinkAddress
          };
          
          console.log('数据转换完成，表单数据:', JSON.stringify(this.form, null, 2));
          console.log('UI配置数据:', JSON.stringify(this.uiConfig, null, 2));
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
  flex-direction: column;
  height: calc(100vh - 120px);
  overflow: hidden;
}

.config-tabs {
  width: 100%;
  border-bottom: 1px solid #e4e7ed;
  background-color: #fff;
}

:deep(.el-menu-demo) {
  background-color: #fff;
  border-bottom: none;
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

:deep(.el-menu-demo .el-menu-item:not(.is-active)) {
  color: #000;
  background-color: #f5f7fa;
}

:deep(.el-menu-demo .el-menu-item:not(.is-active):hover) {
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

/* 首页预览样式 */
.mobile-content {
  width: 100%;
  height: 100%;
  position: relative;
  overflow-y: auto;
  border-radius: 8px;
}

.header {
  width: 100%;
  height: 200px;
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: white;
  text-align: center;
}

.brand-crown {
  font-size: 20px;
  font-weight: bold;
  margin-bottom: 10px;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
}

.promotion-banner {
  background-color: rgba(255, 107, 107, 0.9);
  padding: 10px 20px;
  border-radius: 20px;
  margin-top: 10px;
}

.promotion-text {
  font-size: 18px;
  font-weight: bold;
}

.product-section {
  display: flex;
  justify-content: space-around;
  padding: 20px;
  background-color: rgba(255, 255, 255, 0.8);
  margin: 10px;
  border-radius: 8px;
}

.product-item {
  width: 80px;
  height: 80px;
  border-radius: 4px;
  overflow: hidden;
}

.product-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.action-buttons {
  display: flex;
  justify-content: space-around;
  padding: 15px;
  margin: 10px;
  background-color: rgba(255, 255, 255, 0.8);
  border-radius: 8px;
}

.button {
  padding: 8px 16px;
  border-radius: 15px;
  font-size: 14px;
  font-weight: 500;
}

.customer-button {
  background-color: #409eff;
  color: white;
}

.rule-button {
  background-color: #67c23a;
  color: white;
}

.footer {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  padding: 20px;
  background-color: rgba(255, 255, 255, 0.9);
  display: flex;
  justify-content: center;
}

.action-button {
  padding: 12px 30px;
  border-radius: 25px;
  font-size: 16px;
  font-weight: bold;
  cursor: pointer;
  text-align: center;
}

.config-area {
  flex: 1;
  min-width: 400px;
  border-left: none;
  background-color: #f5f7fa;
  overflow-y: auto;
}

.config-content {
  padding: 20px;
  padding-bottom: 40px;
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