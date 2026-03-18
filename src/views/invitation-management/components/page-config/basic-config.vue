<template>
  <div class="basic-config">
    <el-form :model="form" label-width="120px">
      <el-form-item label="活动名称" required>
        <el-input v-model="form.name" placeholder="请输入活动名称" :disabled="isViewMode"></el-input>
      </el-form-item>
      <el-form-item label="活动编码" required>
        <el-input v-model="form.activity_no" placeholder="请输入活动编码" :disabled="isViewMode"></el-input>
      </el-form-item>
      <el-form-item label="活动类型" required>
        <el-select v-model="form.activity_type" placeholder="选择活动类型" :disabled="isViewMode">
          <el-option label="邀约" value="1"></el-option>
          <el-option label="积赞" value="2"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="关联业务ID">
        <el-input v-model="form.activity_id" placeholder="请输入关联业务ID" :disabled="isViewMode"></el-input>
      </el-form-item>
      <el-form-item label="活动开始时间" required>
        <el-date-picker
          v-model="form.start_time"
          type="datetime"
          placeholder="选择开始时间"
          format="YYYY-MM-DD HH:mm:ss"
          value-format="YYYY-MM-DD HH:mm:ss"
          :disabled="isViewMode"
        ></el-date-picker>
      </el-form-item>
      <el-form-item label="活动结束时间" required>
        <el-date-picker
          v-model="form.end_time"
          type="datetime"
          placeholder="选择结束时间"
          format="YYYY-MM-DD HH:mm:ss"
          value-format="YYYY-MM-DD HH:mm:ss"
          :disabled="isViewMode"
        ></el-date-picker>
      </el-form-item>
      <el-form-item label="载体类型" required>
        <el-select v-model="form.carrier_type" placeholder="选择载体类型" :disabled="isViewMode">
          <el-option label="APP" value="1"></el-option>
          <el-option label="小程序" value="2"></el-option>
          <el-option label="公众号" value="3"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="登录开关">
        <el-radio-group v-model="form.login_switch" :disabled="isViewMode">
          <el-radio label="1">开启</el-radio>
          <el-radio label="2">关闭</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="资料提交开关">
        <el-radio-group v-model="form.data_switch" :disabled="isViewMode">
          <el-radio label="1">开启</el-radio>
          <el-radio label="2">关闭</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="我的客户开关">
        <el-radio-group v-model="form.customer_switch" :disabled="isViewMode">
          <el-radio label="1">开启</el-radio>
          <el-radio label="2">关闭</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="签到开关">
        <el-radio-group v-model="form.sign_in_status" :disabled="isViewMode">
          <el-radio label="1">开启</el-radio>
          <el-radio label="0">关闭</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="分享模式">
        <el-select v-model="form.share_mode" placeholder="选择分享模式" :disabled="isViewMode">
          <el-option label="海报" value="1"></el-option>
          <el-option label="分享" value="2"></el-option>
          <el-option label="邀约码" value="3"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="handleSubmit" :disabled="isViewMode">保存基础设置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import baseService from "@/service/baseService";
import { ElMessage } from 'element-plus';

export default defineComponent({
  name: "BasicConfig",
  props: {
    form: {
      type: Object,
      default: () => ({
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
      })
    },
    isViewMode: {
      type: Boolean,
      default: false
    }
  },
  emits: ['update:form', 'save-success'],
  methods: {
    async handleSubmit() {
      const formData = { ...this.form };
      
      try {
        const url = formData.id 
          ? `${import.meta.env.VITE_APP_API}/api/invitation/update` 
          : `${import.meta.env.VITE_APP_API}/api/invitation/create`;
        
        const res: any = await baseService.post(url, formData);
        if (res.code === '00000') {
          ElMessage.success('基础设置保存成功');
          if (!formData.id) {
            formData.id = res.data.id;
            this.$emit('update:form', formData);
          }
          this.$emit('save-success');
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
.basic-config {
  background-color: #fff;
  padding: 20px;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.basic-config h3 {
  margin-bottom: 20px;
  font-size: 18px;
  font-weight: 600;
}
</style>