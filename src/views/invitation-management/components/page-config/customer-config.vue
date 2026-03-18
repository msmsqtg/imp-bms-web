<template>
  <div class="customer-config">
    <h3>我的客户</h3>
    <el-form :model="uiConfig" label-width="120px">
      <el-form-item label="我的客户设置">
        <el-input type="textarea" v-model="uiConfig.customer_setting" placeholder="我的客户页面视觉设置" :disabled="isViewMode"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="handleSubmit" :disabled="isViewMode">保存我的客户</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import baseService from "@/service/baseService";
import { ElMessage } from 'element-plus';

export default defineComponent({
  name: "CustomerConfig",
  props: {
    uiConfig: {
      type: Object as () => {
        id?: string;
        customer_setting: string;
        [key: string]: any;
      },
      default: () => ({
        id: '',
        customer_setting: ''
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
.customer-config {
  background-color: #fff;
  padding: 20px;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.customer-config h3 {
  margin-bottom: 20px;
  font-size: 18px;
  font-weight: 600;
}
</style>