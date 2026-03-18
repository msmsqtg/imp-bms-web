<template>
  <div class="home-config">
    <h3>首页装修</h3>
    <div class="config-preview-container">
      <div class="config-section">
        <el-form :model="uiConfig" label-width="120px">
          <el-form-item label="主题背景图">
            <div class="upload-container">
              <div class="upload-row">
                <div v-if="uiConfig.theme_bg_pic" class="image-preview">
                  <img :src="uiConfig.theme_bg_pic" alt="主题背景图" class="preview-image">
                </div>
                <div v-else class="upload-placeholder">
                  <div class="placeholder-icon">!</div>
                </div>
                <div v-if="!isViewMode" class="upload-button">
                  <el-button type="primary" plain @click="uploadImage('theme_bg_pic')">+</el-button>
                </div>
              </div>
              <div class="upload-tip">建议尺寸：宽750px 高1334px，格式为jpg/png/gif</div>
            </div>
          </el-form-item>
          <!-- <el-form-item label="模块背景色">
            <el-color-picker v-model="uiConfig.module_bg_color" :disabled="isViewMode"></el-color-picker>
          </el-form-item>
          <el-form-item label="文字颜色">
            <el-color-picker v-model="uiConfig.text_color" :disabled="isViewMode"></el-color-picker>
          </el-form-item> -->
          <el-form-item>
            <el-button type="primary" @click="handleSubmit" :disabled="isViewMode">保存首页装修</el-button>
          </el-form-item>
        </el-form>
      </div>
      <div class="preview-section">
        <h4>首页预览</h4>
        <div class="mobile-preview">
          <div class="mobile-frame">
            <div class="mobile-content" :style="{ backgroundColor: uiConfig.module_bg_color || '#f5f5f5' }">
              <div class="header" :style="{ background: uiConfig.theme_bg_pic ? `url(${uiConfig.theme_bg_pic}) no-repeat center center / cover` : '#ff6b6b' }">
              </div>
              <div class="action-buttons">
                <div class="button customer-button">我的客户</div>
                <div class="button rule-button">活动规则</div>
              </div>
              <div class="footer">
                <div class="action-button" :style="{ backgroundColor: uiConfig.module_bg_color || '#ff6b6b', color: uiConfig.text_color || '#fff' }">立即领取</div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import baseService from "@/service/baseService";
import { ElMessage } from 'element-plus';

export default defineComponent({
  name: "HomeConfig",
  props: {
    uiConfig: {
      type: Object as () => {
        id?: string;
        store_id?: number;
        invitation_id?: string;
        theme_bg_pic?: string;
        home_bg_pic?: string;
        module_bg_color?: string;
        text_color?: string;
        share_poster_pic?: string;
        share_bg_setting?: string;
        poster_pic_text?: string;
        share_text?: string;
        login_setting?: string;
        data_setting?: string;
        c_data_setting?: string;
        customer_setting?: string;
        sign_in_setting?: string;
        invitation_rule_setting?: string;
        third_link_address?: string;
        [key: string]: any;
      },
      default: () => ({
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
      })
    },
    invitationId: {
      type: String,
      default: ''
    },
    isViewMode: {
      type: Boolean,
      default: false
    }
  },
  emits: ['update:uiConfig'],
  methods: {
    async uploadImage(field: 'theme_bg_pic' | 'home_bg_pic') {
      const input = document.createElement('input');
      input.type = 'file';
      input.accept = 'image/*';
      input.onchange = async (e) => {
        const target = e.target as HTMLInputElement;
        if (target.files && target.files[0]) {
          const file = target.files[0];
          const formData = new FormData();
          formData.append('file', file);

          try {
            const res: any = await baseService.post('/common/upload/image', formData);
            if (res.code === '00000' && res.data && res.data.url) {
              const updatedConfig = { ...this.uiConfig, [field]: res.data.url };
              this.$emit('update:uiConfig', updatedConfig);
            } else {
              ElMessage.error('图片上传失败，请重试');
            }
          } catch (error) {
            console.error('图片上传失败:', error);
            ElMessage.error('图片上传失败，请重试');
          }
        }
      };
      input.click();
    },

    async handleSubmit() {
      if (!this.invitationId) {
        ElMessage.error('请先保存基础设置');
        return;
      }

      const configData = { ...this.uiConfig, invitation_id: this.invitationId };
      
      try {
        const url = configData.id != null 
          ? `${import.meta.env.VITE_APP_API}/invitation/ui/update` 
          : `${import.meta.env.VITE_APP_API}/invitation/ui/create`;
        
        const res: any = await baseService.post(url, configData);
        if (res.code === '00000') {
          ElMessage.success('保存成功');
          if (configData.id == null) {
            configData.id = res.data.id;
            this.$emit('update:uiConfig', configData);
          }
        } else {
          ElMessage.error('保存失败：' + res.msg);
        }
      } catch (error) {
        console.error('保存失败:', error);
        ElMessage.error('保存失败，请重试');
      }
    }
  }
});
</script>

<style scoped>
.home-config {
  background-color: #fff;
  padding: 20px;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.home-config h3 {
  margin-bottom: 20px;
  font-size: 18px;
  font-weight: 600;
}

.config-preview-container {
  display: flex;
  gap: 30px;
}

.config-section {
  flex: 1;
  min-width: 400px;
}

.preview-section {
  flex: 1;
  min-width: 300px;
}

.preview-section h4 {
  margin-bottom: 15px;
  font-size: 16px;
  font-weight: 500;
  color: #303133;
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

.mobile-preview {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
  background-color: #f5f7fa;
  border-radius: 8px;
}

.mobile-frame {
  width: 280px;
  height: 560px;
  border: 10px solid #333;
  border-radius: 30px;
  overflow: hidden;
  position: relative;
  background-color: #fff;
}

.mobile-frame::before {
  content: '';
  position: absolute;
  top: -5px;
  left: 50%;
  transform: translateX(-50%);
  width: 120px;
  height: 20px;
  background-color: #333;
  border-radius: 10px;
}

.mobile-content {
  width: 100%;
  height: 100%;
  position: relative;
  overflow-y: auto;
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

@media (max-width: 1024px) {
  .config-preview-container {
    flex-direction: column;
  }
  
  .config-section,
  .preview-section {
    min-width: auto;
  }
}
</style>