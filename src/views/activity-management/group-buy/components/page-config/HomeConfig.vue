<template>
  <div class="home-config">
    <div class="preview-section">
      <h4>预览</h4>
      <div class="preview-content">
        <div class="preview-header" v-if="form.headerImage">
          <img :src="form.headerImage" alt="页面头图">
        </div>
        <div class="preview-header-placeholder" v-else>
          <div class="placeholder-text">页面头图</div>
        </div>

        <div class="preview-products">
          <div class="product-card" v-for="(product, index) in previewProducts" :key="index">
            <div class="product-image">
              <img :src="product.thumb" v-if="product.thumb">
              <div class="product-placeholder" v-else>商品图</div>
            </div>
            <div class="product-info">
              <div class="product-title">{{ product.name || '商品标题' }}</div>
              <div class="product-price">
                <span class="group-price">¥{{ product.teamPrice || '0.00' }}</span>
                <span class="original-price">¥{{ product.price || '0.00' }}</span>
              </div>
            </div>
            <div class="product-btn">我要开团</div>
          </div>
        </div>

        <div class="preview-group">
          <div class="group-title">进行中</div>
          <div class="group-card">
            <div class="group-image">
              <img :src="previewProducts[0]?.thumb" v-if="previewProducts[0]?.thumb">
              <div class="group-placeholder" v-else>商品图</div>
            </div>
            <div class="group-info">
              <div class="group-product-title">{{ previewProducts[0]?.name || '商品标题' }}</div>
              <div class="group-price">成团价 ¥{{ previewProducts[0]?.teamPrice || '0.00' }}</div>
            </div>
            <div class="group-btn">邀请好友</div>
          </div>
        </div>
      </div>
    </div>

    <div class="form-section">
      <el-form :model="form" label-width="100px">
        <el-form-item label="页面头图:">
          <div class="upload-wrapper">
            <div class="image-preview" v-if="form.headerImage">
              <img :src="form.headerImage" alt="页面头图">
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
          <div class="upload-tip">备注：多图，图片建议尺寸宽度为750px</div>
        </el-form-item>

        <el-form-item label="页面背景图:">
          <div class="upload-wrapper">
            <div class="image-preview bg-preview" v-if="form.backgroundImage">
              <img :src="form.backgroundImage" alt="页面背景图">
              <div class="image-actions">
                <el-button type="danger" size="small" circle @click="form.backgroundImage = ''">
                  <el-icon><Delete /></el-icon>
                </el-button>
              </div>
            </div>
            <div class="upload-box bg-upload" v-else @click="uploadImage('backgroundImage')">
              <el-icon><Plus /></el-icon>
            </div>
          </div>
          <div class="upload-tip">建议尺寸：宽750px，格式为jpg/png/gif</div>
        </el-form-item>

        <el-form-item label="跑马灯:">
          <el-radio-group v-model="form.marquee">
            <el-radio label="hide">隐藏</el-radio>
            <el-radio label="show">展示</el-radio>
          </el-radio-group>
        </el-form-item>

        <el-form-item label="活动规则:">
          <el-radio-group v-model="form.activityRules">
            <el-radio label="hide">隐藏</el-radio>
            <el-radio label="show">展示</el-radio>
          </el-radio-group>
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
  name: 'HomeConfig',
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
      backgroundImage: '',
      marquee: 'show',
      activityRules: 'hide'
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
      console.log('保存首页配置', form.value);
      // TODO: 调用首页配置保存接口
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
.home-config {
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
}

.preview-header {
  width: 100%;
  height: 150px;
  overflow: hidden;
}

.preview-header img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.preview-header-placeholder {
  width: 100%;
  height: 150px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  display: flex;
  align-items: center;
  justify-content: center;
}

.placeholder-text {
  color: #fff;
  font-size: 16px;
}

.preview-products {
  padding: 10px;
}

.product-card {
  background: #fff;
  border-radius: 8px;
  padding: 10px;
  margin-bottom: 10px;
  display: flex;
  align-items: center;
  gap: 8px;
}

.product-image {
  width: 60px;
  height: 60px;
  border-radius: 6px;
  overflow: hidden;
  flex-shrink: 0;
}

.product-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.product-placeholder {
  width: 100%;
  height: 100%;
  background: #f0f0f0;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #999;
  font-size: 11px;
}

.product-info {
  flex: 1;
}

.product-title {
  font-size: 12px;
  color: #333;
  margin-bottom: 5px;
  line-height: 1.3;
}

.product-price {
  display: flex;
  align-items: center;
  gap: 5px;
}

.group-price {
  color: #f56c6c;
  font-size: 14px;
  font-weight: bold;
}

.original-price {
  color: #999;
  font-size: 11px;
  text-decoration: line-through;
}

.product-btn {
  background: linear-gradient(135deg, #ff6b6b 0%, #ee5a6f 100%);
  color: #fff;
  padding: 6px 12px;
  border-radius: 15px;
  font-size: 11px;
  white-space: nowrap;
}

.preview-group {
  padding: 0 10px 10px;
}

.group-title {
  background: linear-gradient(135deg, #ff6b6b 0%, #ee5a6f 100%);
  color: #fff;
  text-align: center;
  padding: 6px;
  border-radius: 8px 8px 0 0;
  font-size: 12px;
}

.group-card {
  background: #fff;
  border-radius: 0 0 8px 8px;
  padding: 10px;
  display: flex;
  align-items: center;
  gap: 8px;
}

.group-image {
  width: 50px;
  height: 50px;
  border-radius: 6px;
  overflow: hidden;
  flex-shrink: 0;
}

.group-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.group-placeholder {
  width: 100%;
  height: 100%;
  background: #f0f0f0;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #999;
  font-size: 11px;
}

.group-info {
  flex: 1;
}

.group-product-title {
  font-size: 11px;
  color: #333;
  margin-bottom: 3px;
}

.group-price {
  color: #f56c6c;
  font-size: 12px;
}

.group-btn {
  background: linear-gradient(135deg, #ff6b6b 0%, #ee5a6f 100%);
  color: #fff;
  padding: 5px 10px;
  border-radius: 12px;
  font-size: 10px;
  white-space: nowrap;
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

.image-preview.bg-preview {
  width: 80px;
  height: 80px;
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

.upload-box.bg-upload {
  width: 80px;
  height: 80px;
}

.upload-tip {
  margin-top: 8px;
  color: #909399;
  font-size: 12px;
}
</style>
