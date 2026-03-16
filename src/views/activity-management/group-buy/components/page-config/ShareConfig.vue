<template>
  <div class="share-config">
    <div class="preview-section">
      <h4>预览</h4>
      <div class="preview-content">
        <div class="share-preview">
          <div class="share-header">分享给好友</div>
          <div class="share-content">
            <div class="share-product">
              <div class="share-image">
                <img :src="previewProducts[0]?.thumb" v-if="previewProducts[0]?.thumb">
                <div class="share-placeholder" v-else>商品图</div>
              </div>
              <div class="share-info">
                <div class="share-title">{{ form.title || previewProducts[0]?.name || '商品标题' }}</div>
                <div class="share-price">¥{{ previewProducts[0]?.teamPrice || '0.00' }}</div>
              </div>
            </div>
            <div class="share-tip">{{ form.description || '快来帮我砍价吧！' }}</div>
            <div class="share-platforms">
              <div class="platform-item">微信好友</div>
              <div class="platform-item">朋友圈</div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="form-section">
      <el-form :model="form" label-width="100px">
        <el-form-item label="分享标题:">
          <el-input v-model="form.title" placeholder="请输入分享标题"></el-input>
        </el-form-item>

        <el-form-item label="分享描述:">
          <el-input v-model="form.description" placeholder="请输入分享描述"></el-input>
        </el-form-item>

        <el-form-item label="分享图片:">
          <div class="upload-wrapper">
            <div class="image-preview" v-if="form.shareImage">
              <img :src="form.shareImage" alt="分享图片">
              <div class="image-actions">
                <el-button type="danger" size="small" circle @click="form.shareImage = ''">
                  <el-icon><Delete /></el-icon>
                </el-button>
              </div>
            </div>
            <div class="upload-box" v-else @click="uploadImage('shareImage')">
              <el-icon><Plus /></el-icon>
            </div>
          </div>
          <div class="upload-tip">建议尺寸：宽500px 高400px</div>
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
  name: 'ShareConfig',
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
      title: '',
      description: '',
      shareImage: ''
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
      console.log('保存分享效果配置', form.value);
      // TODO: 调用分享效果配置保存接口
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
.share-config {
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

.share-preview {
  background: #fff;
  border-radius: 8px;
  padding: 15px;
}

.share-header {
  font-size: 16px;
  font-weight: bold;
  text-align: center;
  margin-bottom: 15px;
  color: #333;
}

.share-content {
  background: #f9f9f9;
  border-radius: 8px;
  padding: 10px;
}

.share-product {
  display: flex;
  gap: 10px;
  margin-bottom: 15px;
}

.share-image {
  width: 70px;
  height: 70px;
  border-radius: 6px;
  overflow: hidden;
}

.share-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.share-placeholder {
  width: 100%;
  height: 100%;
  background: #f0f0f0;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #999;
  font-size: 11px;
}

.share-info {
  flex: 1;
}

.share-title {
  font-size: 13px;
  color: #333;
  margin-bottom: 8px;
}

.share-price {
  color: #f56c6c;
  font-size: 16px;
  font-weight: bold;
}

.share-tip {
  text-align: center;
  font-size: 12px;
  color: #666;
  margin-bottom: 15px;
}

.share-platforms {
  display: flex;
  gap: 10px;
}

.platform-item {
  flex: 1;
  background: #f0f0f0;
  padding: 8px;
  border-radius: 5px;
  text-align: center;
  font-size: 11px;
  color: #666;
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
