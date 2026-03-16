<template>
  <div class="friend-config">
    <div class="preview-section">
      <h4>预览</h4>
      <div class="preview-content">
        <div class="friend-preview">
          <div class="friend-header">好友助力</div>
          <div class="friend-content">
            <div class="friend-avatar">
              <div class="avatar-placeholder">头像</div>
            </div>
            <div class="friend-text">{{ form.helpText || '邀请您助力拼团' }}</div>
            <div class="friend-product">
              <div class="friend-image">
                <img :src="previewProducts[0]?.thumb" v-if="previewProducts[0]?.thumb">
                <div class="friend-placeholder" v-else>商品图</div>
              </div>
              <div class="friend-info">
                <div class="friend-title">{{ previewProducts[0]?.name || '商品标题' }}</div>
                <div class="friend-price">¥{{ previewProducts[0]?.teamPrice || '0.00' }}</div>
              </div>
            </div>
            <div class="friend-btn">立即助力</div>
          </div>
        </div>
      </div>
    </div>

    <div class="form-section">
      <el-form :model="form" label-width="100px">
        <el-form-item label="助力头图:">
          <div class="upload-wrapper">
            <div class="image-preview" v-if="form.headerImage">
              <img :src="form.headerImage" alt="助力头图">
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

        <el-form-item label="助力文案:">
          <el-input v-model="form.helpText" placeholder="请输入助力文案"></el-input>
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
  name: 'FriendConfig',
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
      helpText: '邀请您助力拼团'
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
      console.log('保存好友进入配置', form.value);
      // TODO: 调用好友进入配置保存接口
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
.friend-config {
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

.friend-preview {
  background: #fff;
  border-radius: 8px;
  padding: 15px;
}

.friend-header {
  font-size: 16px;
  font-weight: bold;
  text-align: center;
  margin-bottom: 15px;
  color: #333;
}

.friend-content {
  text-align: center;
}

.friend-avatar {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background: #f0f0f0;
  margin: 0 auto 12px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.avatar-placeholder {
  color: #999;
  font-size: 11px;
}

.friend-text {
  font-size: 12px;
  color: #666;
  margin-bottom: 15px;
}

.friend-product {
  background: #f9f9f9;
  border-radius: 8px;
  padding: 10px;
  display: flex;
  gap: 8px;
  margin-bottom: 15px;
}

.friend-image {
  width: 50px;
  height: 50px;
  border-radius: 6px;
  overflow: hidden;
}

.friend-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.friend-placeholder {
  width: 100%;
  height: 100%;
  background: #f0f0f0;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #999;
  font-size: 11px;
}

.friend-info {
  flex: 1;
  text-align: left;
}

.friend-title {
  font-size: 12px;
  color: #333;
  margin-bottom: 8px;
}

.friend-price {
  color: #f56c6c;
  font-size: 14px;
  font-weight: bold;
}

.friend-btn {
  background: linear-gradient(135deg, #ff6b6b 0%, #ee5a6f 100%);
  color: #fff;
  padding: 10px 30px;
  border-radius: 20px;
  font-size: 12px;
  display: inline-block;
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
