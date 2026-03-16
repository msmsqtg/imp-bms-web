<template>
  <div class="prize-config">
    <div class="preview-section">
      <h4>预览</h4>
      <div class="preview-content">
        <div class="prize-preview">
          <div class="prize-header">恭喜您获得奖品</div>
          <div class="prize-content">
            <div class="prize-icon">🎉</div>
            <div class="prize-name">{{ form.congratsText || '助力奖品' }}</div>
            <div class="prize-desc">感谢您的参与</div>
            <div class="prize-btn">查看奖品</div>
          </div>
        </div>
      </div>
    </div>

    <div class="form-section">
      <el-form :model="form" label-width="100px">
        <el-form-item label="获奖头图:">
          <div class="upload-wrapper">
            <div class="image-preview" v-if="form.headerImage">
              <img :src="form.headerImage" alt="获奖头图">
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

        <el-form-item label="获奖文案:">
          <el-input v-model="form.congratsText" placeholder="请输入获奖文案"></el-input>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import { Plus, Delete } from '@element-plus/icons-vue';

export default defineComponent({
  name: 'PrizeConfig',
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
      congratsText: '恭喜您获得奖品'
    });

    const uploadImage = (field: string) => {
      console.log(`上传图片 - field: ${field}`);
    };

    const save = async () => {
      console.log('保存好友获奖配置', form.value);
      // TODO: 调用好友获奖配置保存接口
    };

    return {
      form,
      uploadImage,
      save
    };
  }
});
</script>

<style scoped>
.prize-config {
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

.prize-preview {
  background: #fff;
  border-radius: 8px;
  padding: 15px;
  text-align: center;
}

.prize-header {
  font-size: 16px;
  font-weight: bold;
  color: #333;
  margin-bottom: 20px;
}

.prize-content {
  background: #f9f9f9;
  border-radius: 8px;
  padding: 20px 15px;
}

.prize-icon {
  font-size: 40px;
  margin-bottom: 15px;
}

.prize-name {
  font-size: 14px;
  color: #333;
  margin-bottom: 8px;
}

.prize-desc {
  font-size: 12px;
  color: #999;
  margin-bottom: 15px;
}

.prize-btn {
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
