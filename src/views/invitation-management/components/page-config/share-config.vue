<template>
  <div class="share-config">
    <h3>分享设置</h3>
    <el-form :model="uiConfig" label-width="120px">
      <el-form-item label="分享海报图">
        <div class="upload-container">
          <div class="upload-row">
            <div v-if="uiConfig.share_poster_pic" class="image-preview">
              <img :src="uiConfig.share_poster_pic" alt="分享海报图" class="preview-image">
            </div>
            <div v-else class="upload-placeholder">
              <div class="placeholder-icon">!</div>
            </div>
            <div v-if="!isViewMode" class="upload-button">
              <el-button type="primary" plain @click="uploadImage('share_poster_pic')">+</el-button>
            </div>
          </div>
          <div class="upload-tip">建议尺寸：宽750px 高1000px，格式为jpg/png/gif</div>
        </div>
      </el-form-item>
      <el-form-item label="海报参数配置">
        <el-input type="textarea" v-model="uiConfig.share_bg_setting" placeholder="海报参数配置" :disabled="isViewMode"></el-input>
      </el-form-item>
      <el-form-item label="海报文案">
        <el-input type="textarea" v-model="uiConfig.poster_pic_text" placeholder="海报文案" :disabled="isViewMode"></el-input>
      </el-form-item>
      <el-form-item label="分享文案">
        <el-input type="textarea" v-model="uiConfig.share_text" placeholder="分享文案" :disabled="isViewMode"></el-input>
      </el-form-item>
      <el-form-item label="第三方链接地址">
        <el-input v-model="uiConfig.third_link_address" placeholder="请输入第三方链接地址" :disabled="isViewMode"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="handleSubmit" :disabled="isViewMode">保存分享设置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import baseService from "@/service/baseService";
import { ElMessage } from 'element-plus';

export default defineComponent({
  name: "ShareConfig",
  props: {
    uiConfig: {
      type: Object as () => {
        id?: string;
        share_poster_pic?: string;
        share_bg_setting?: string;
        poster_pic_text?: string;
        share_text?: string;
        third_link_address?: string;
        [key: string]: any;
      },
      default: () => ({
        id: '',
        share_poster_pic: '',
        share_bg_setting: '',
        poster_pic_text: '',
        share_text: '',
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
    async uploadImage(field: 'share_poster_pic') {
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
.share-config {
  background-color: #fff;
  padding: 20px;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.share-config h3 {
  margin-bottom: 20px;
  font-size: 18px;
  font-weight: 600;
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
</style>