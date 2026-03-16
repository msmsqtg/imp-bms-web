<template>
  <div class="detail-config">
    <div class="preview-section">
      <h4>预览</h4>
      <div class="preview-content">
        <div class="detail-preview">
          <div class="detail-header">拼团详情</div>
          <div class="detail-content">
            <div class="detail-product">
              <div class="detail-image">
                <img :src="previewProducts[0]?.thumb" v-if="previewProducts[0]?.thumb">
                <div class="detail-placeholder" v-else>商品图</div>
              </div>
              <div class="detail-info">
                <div class="detail-title">{{ previewProducts[0]?.name || '商品标题' }}</div>
                <div class="detail-price">¥{{ previewProducts[0]?.teamPrice || '0.00' }}</div>
              </div>
            </div>
            <div class="detail-progress">
              <div class="progress-text">拼团进度</div>
              <div class="progress-bar">
                <div class="progress-fill" style="width: 60%"></div>
              </div>
              <div class="progress-info">还差1人成团</div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="form-section">
      <el-form :model="form" label-width="100px">
        <el-form-item label="详情头图:">
          <div class="upload-wrapper">
            <div class="image-preview" v-if="form.headerImage">
              <img :src="form.headerImage" alt="详情头图">
              <div class="image-actions">
                <el-button type="danger" size="small" circle @click="form.headerImage = ''">
                  <el-icon><Delete /></el-icon>
                </el-button>
              </div>
            </div>
            <div class="upload-box" v-else @click="uploadImage('headerImage')">
              <el-icon><Plus /></el-icon>
            </div>
          </div>
          <div class="upload-tip">建议尺寸：宽750px 高200px</div>
        </el-form-item>

        <el-form-item label="背景颜色:">
          <el-color-picker v-model="form.backgroundColor"></el-color-picker>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue';
import { Plus, Delete } from '@element-plus/icons-vue';
import baseService from "@/service/baseService";

export default defineComponent({
  name: 'DetailConfig',
  components: {
    Plus,
    Delete
  },
  props: {
    impId: {
      type: Number,
      required: true
    }
  },
  setup(props) {
    const form = ref({
      headerImage: '',
      backgroundColor: '#ffffff'
    });

    const previewProducts = ref<any[]>([]);

    const loadProducts = async () => {
      try {
        const requestUrl = `${import.meta.env.VITE_APP_API}/team/buy/detail/prize/list`;
        const params = { impId: props.impId };
        const headers = { createUserId: 4440 };

        const res: any = await baseService.get(requestUrl, params, headers);
        if (res.code === '00000') {
          previewProducts.value = res.data.data.filter((item: any) => item.prizeType === 1);
        }
      } catch (error) {
        console.error('获取商品数据失败:', error);
      }
    };

    const uploadImage = (field: string) => {
      console.log(`上传图片 - field: ${field}`);
    };

    const save = async () => {
      console.log('保存拼团详情配置', form.value);
      // TODO: 调用拼团详情配置保存接口
    };

    onMounted(() => {
      loadProducts();
    });

    return {
      form,
      previewProducts,
      uploadImage,
      save
    };
  }
});
</script>

<style scoped>
.detail-config {
  display: flex;
  gap: 20px;
}

.preview-section {
  flex: 1;
  background: #f5f7fa;
  padding: 15px;
  border-radius: 4px;
}

.preview-section h4 {
  margin-bottom: 15px;
  font-size: 14px;
  color: #666;
}

.preview-content {
  background: #e8f5e9;
  border-radius: 10px;
  overflow: hidden;
  padding: 15px;
}

.detail-preview {
  background: #fff;
  border-radius: 8px;
  padding: 15px;
}

.detail-header {
  font-size: 16px;
  font-weight: bold;
  text-align: center;
  margin-bottom: 15px;
  color: #333;
}

.detail-content {
  background: #fff;
  border-radius: 8px;
  padding: 10px;
}

.detail-product {
  display: flex;
  gap: 10px;
  margin-bottom: 15px;
}

.detail-image {
  width: 70px;
  height: 70px;
  border-radius: 6px;
  overflow: hidden;
}

.detail-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.detail-placeholder {
  width: 100%;
  height: 100%;
  background: #f0f0f0;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #999;
  font-size: 11px;
}

.detail-info {
  flex: 1;
}

.detail-title {
  font-size: 13px;
  color: #333;
  margin-bottom: 8px;
}

.detail-price {
  color: #f56c6c;
  font-size: 16px;
  font-weight: bold;
}

.detail-progress {
  text-align: center;
}

.progress-text {
  font-size: 12px;
  color: #666;
  margin-bottom: 8px;
}

.progress-bar {
  height: 8px;
  background: #f0f0f0;
  border-radius: 4px;
  overflow: hidden;
  margin-bottom: 8px;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(135deg, #ff6b6b 0%, #ee5a6f 100%);
}

.progress-info {
  font-size: 11px;
  color: #999;
}

.form-section {
  width: 300px;
  background: #fff;
  padding: 15px;
  border-radius: 4px;
  border: 1px solid #e4e7ed;
}

.upload-wrapper {
  display: flex;
  flex-direction: column;
}

.image-preview {
  position: relative;
  width: 120px;
  height: 120px;
  border-radius: 4px;
  overflow: hidden;
  border: 1px solid #e4e7ed;
}

.image-preview img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.image-actions {
  position: absolute;
  top: 5px;
  right: 5px;
}

.upload-box {
  width: 120px;
  height: 120px;
  border: 2px dashed #d9d9d9;
  border-radius: 4px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: border-color 0.3s;
}

.upload-box:hover {
  border-color: #409eff;
}

.upload-box .el-icon {
  font-size: 24px;
  color: #8c939d;
}

.upload-tip {
  margin-top: 8px;
  color: #909399;
  font-size: 12px;
}
</style>
