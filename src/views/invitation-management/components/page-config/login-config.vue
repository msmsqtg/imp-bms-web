<template>
  <div class="login-config">
    <el-form :model="uiConfig" label-width="120px">
      <el-form-item label="登录设置">
        <div class="login-switch">
          <el-radio-group v-model="loginSwitch" :disabled="isViewMode">
            <el-radio label="1">需要登录</el-radio>
            <el-radio label="2">无需登录</el-radio>
          </el-radio-group>
        </div>
      </el-form-item>
      
      <el-form-item label="登录表单配置" required>
        <div class="login-form-config">
          <div class="form-header">
            <div class="form-checkbox">
              <el-checkbox v-model="selectAll" @change="handleSelectAll">全选</el-checkbox>
            </div>
            <div class="form-type">表单类型</div>
            <div class="form-title">标题文案</div>
            <div class="form-required">是否必填</div>
            <div class="form-unique">唯一ID标记</div>
            <div class="form-whitelist">是否需要白名单验证</div>
          </div>
          <div class="form-item" v-for="(item, index) in formItems" :key="index">
            <div class="form-checkbox">
              <el-checkbox v-model="item.choose_switch" :disabled="isViewMode"></el-checkbox>
            </div>
            <div class="form-type">{{ item.form_type_text }}</div>
            <div class="form-title">
              <el-input v-model="item.form_name" size="small" :disabled="isViewMode || !item.choose_switch"></el-input>
            </div>
            <div class="form-required">
              <el-checkbox v-model="item.required_switch" :disabled="isViewMode || !item.choose_switch">{{ item.required_switch ? '必填' : '' }}</el-checkbox>
            </div>
            <div class="form-unique">
              <el-radio v-model="uniqueId" :label="item.params_name" :disabled="isViewMode || !item.choose_switch">{{ item.choose_switch ? '唯一ID' : '' }}</el-radio>
            </div>
            <div class="form-whitelist">
              <el-checkbox v-model="item.check_switch" :disabled="isViewMode || !item.choose_switch">{{ item.check_switch ? '白名单验证' : '' }}</el-checkbox>
            </div>
          </div>
        </div>
      </el-form-item>
      
      <el-form-item>
        <el-button type="primary" @click="handleSubmit" :disabled="isViewMode">保存登录页面</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch, computed } from 'vue';
import baseService from "@/service/baseService";
import { ElMessage } from 'element-plus';

export default defineComponent({
  name: "LoginConfig",
  props: {
    form: {
      type: Object as () => {
        login_form: string;
        [key: string]: any;
      },
      default: () => ({
        login_form: ''
      })
    },
    uiConfig: {
      type: Object as () => {
        id?: string;
        login_setting: string;
        [key: string]: any;
      },
      default: () => ({
        id: '',
        login_setting: ''
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
  emits: ['update:form', 'update:uiConfig'],
  setup(props, { emit }) {
    // 定义固定的表单字段类型
    const formItems = ref([
      {
        form_name: '姓名',
        form_type: 'name',
        form_type_text: '姓名',
        only_switch: 0,
        params_name: 'name',
        check_switch: 1,
        choose_switch: 1,
        required_switch: 1
      },
      {
        form_name: '手机号码',
        form_type: 'phone_no',
        form_type_text: '手机号码',
        only_switch: 1,
        params_name: 'phone_no',
        check_switch: 1,
        choose_switch: 1,
        required_switch: 1
      },
      {
        form_name: '验证码',
        form_type: 'verification_code',
        form_type_text: '验证码',
        only_switch: 0,
        params_name: 'verification_code',
        check_switch: 0,
        choose_switch: 2,
        required_switch: 2
      },
      {
        form_name: '身份证号码',
        form_type: 'id_card_no',
        form_type_text: '身份证号码',
        only_switch: 2,
        params_name: 'id_card_no',
        check_switch: 2,
        choose_switch: 2,
        required_switch: 2
      },
      {
        form_name: '工号',
        form_type: 'numeric_word',
        form_type_text: '数字编号',
        only_switch: 2,
        params_name: 'numeric_word',
        check_switch: 1,
        choose_switch: 1,
        required_switch: 1
      },
      {
        form_name: '字符串',
        form_type: 'string_word',
        form_type_text: '字符串',
        only_switch: 0,
        params_name: 'string_word',
        check_switch: 0,
        choose_switch: 2,
        required_switch: 2
      },
      {
        form_name: '机构名称',
        form_type: 'organization_name',
        form_type_text: '机构名称',
        only_switch: 0,
        params_name: 'organization_name',
        check_switch: 0,
        choose_switch: 2,
        required_switch: 2
      }
    ]);

    const uniqueId = ref('phone_no'); // 默认唯一ID为手机号码
    const loginSwitch = ref('1'); // 默认需要登录

    // 从 props.form 中解析数据
    watch(() => props.form, (newVal) => {
      if (newVal) {
        // 解析登录开关
        if (newVal.login_switch) {
          loginSwitch.value = newVal.login_switch.toString();
        }
        
        // 解析登录表单数据
        if (newVal.login_form) {
          try {
            const loginFormData = JSON.parse(newVal.login_form);
            loginFormData.forEach((item: any) => {
              const formItem = formItems.value.find(fi => fi.form_type === item.form_type);
              if (formItem) {
                formItem.form_name = item.form_name;
                formItem.only_switch = item.only_switch;
                formItem.check_switch = item.check_switch;
                formItem.choose_switch = item.choose_switch;
                formItem.required_switch = item.required_switch;
                if (item.only_switch === 1) {
                  uniqueId.value = item.params_name;
                }
              }
            });
          } catch (error) {
            console.error('解析登录表单数据失败:', error);
          }
        }
      }
    }, { immediate: true, deep: true });

    // 全选/取消全选
    const selectAll = computed({
      get: () => {
        return formItems.value.every(item => item.choose_switch === 1);
      },
      set: (value) => {
        formItems.value.forEach(item => {
          item.choose_switch = value ? 1 : 2;
        });
      }
    });

    // 处理全选
    const handleSelectAll = (value: boolean) => {
      formItems.value.forEach(item => {
        item.choose_switch = value ? 1 : 2;
      });
    };

    // 处理提交
    const handleSubmit = async () => {
      if (!props.invitationId) {
        ElMessage.error('请先保存基础设置');
        return;
      }

      // 构建登录表单数据
      const loginFormData = formItems.value.map(item => ({
        form_name: item.form_name,
        form_type: item.form_type,
        only_switch: item.params_name === uniqueId.value ? 1 : 0,
        params_name: item.params_name,
        check_switch: item.check_switch,
        choose_switch: item.choose_switch,
        required_switch: item.required_switch
      }));

      const loginFormStr = JSON.stringify(loginFormData);

      // 保存基础设置中的登录表单字段和登录开关
      try {
        const baseUrl = `${import.meta.env.VITE_APP_API}/api/invitation/update`;
        const res: any = await baseService.post(baseUrl, {
          id: props.invitationId,
          login_form: loginFormStr,
          login_switch: parseInt(loginSwitch.value)
        });
        if (res.code !== '00000') {
          ElMessage.error('保存登录表单字段失败：' + res.msg);
          return;
        }
      } catch (error) {
        console.error('保存登录表单字段失败:', error);
        ElMessage.error('保存登录表单字段失败，请重试');
        return;
      }

      // 保存UI配置
      const configData = { ...props.uiConfig, invitation_id: props.invitationId };
      
      try {
        const url = configData.id != null 
          ? `${import.meta.env.VITE_APP_API}/api/invitation/ui/update` 
          : `${import.meta.env.VITE_APP_API}/api/invitation/ui/create`;
        
        const res: any = await baseService.post(url, configData);
        if (res.code === '00000') {
          ElMessage.success('保存成功');
          if (configData.id == null) {
            configData.id = res.data.id;
            emit('update:uiConfig', configData);
          }
          // 更新父组件的 form
          emit('update:form', { 
            ...props.form, 
            login_form: loginFormStr,
            login_switch: parseInt(loginSwitch.value)
          });
        } else {
          ElMessage.error('保存失败：' + res.msg);
        }
      } catch (error) {
        console.error('保存失败:', error);
        ElMessage.error('保存失败，请重试');
      }
    };

    return {
      formItems,
      uniqueId,
      loginSwitch,
      selectAll,
      handleSelectAll,
      handleSubmit
    };
  }
});
</script>

<style scoped>
.login-config {
  background-color: #fff;
  padding: 20px;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.login-config h3 {
  margin-bottom: 20px;
  font-size: 18px;
  font-weight: 600;
}

.login-form-config {
  border: 1px solid #e4e7ed;
  border-radius: 4px;
  overflow: hidden;
}

.form-header {
  display: flex;
  background-color: #f5f7fa;
  padding: 10px;
  font-weight: 600;
  border-bottom: 1px solid #e4e7ed;
}

.form-item {
  display: flex;
  padding: 10px;
  border-bottom: 1px solid #e4e7ed;
  align-items: center;
}

.form-item:last-child {
  border-bottom: none;
}

.form-checkbox {
  width: 80px;
}

.form-type {
  width: 100px;
}

.form-title {
  width: 150px;
}

.form-required {
  width: 100px;
}

.form-unique {
  width: 120px;
}

.form-whitelist {
  flex: 1;
}

.el-input {
  width: 120px;
}
</style>