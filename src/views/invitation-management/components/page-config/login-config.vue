<template>
  <div class="login-config">
    <el-form :model="uiConfig" label-width="120px">
      <el-form-item label="登录设置">
        <div class="login-switch">
          <el-radio-group v-model="loginSwitch" :disabled="isViewMode" @change="handleLoginSwitchChange">
            <el-radio label="1">需要登录</el-radio>
            <el-radio label="2">无需登录</el-radio>
          </el-radio-group>
        </div>
      </el-form-item>
      
      <el-form-item label="登录表单配置" required v-if="loginSwitch === '1'">
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
              <el-checkbox v-model="item.choose_switch" :true-label="1" :false-label="2" :disabled="isViewMode" @change="handleFormChange"></el-checkbox>
            </div>
            <div class="form-type">{{ item.form_type_text }}</div>
            <div class="form-title">
              <el-input v-model="item.form_name" size="small" :disabled="isViewMode || item.choose_switch !== 1" @input="handleFormChange"></el-input>
            </div>
            <div class="form-required">
              <el-checkbox v-model="item.required_switch" :true-label="1" :false-label="2" :disabled="isViewMode || item.choose_switch !== 1" @change="handleFormChange">{{ item.required_switch === 1 ? '必填' : '' }}</el-checkbox>
            </div>
            <div class="form-unique">
              <el-radio v-model="uniqueId" :label="item.params_name" :disabled="isViewMode || item.choose_switch !== 1" @change="handleFormChange">{{ item.choose_switch === 1 ? '唯一ID' : '' }}</el-radio>
            </div>
            <div class="form-whitelist">
              <el-checkbox v-model="item.check_switch" :true-label="1" :false-label="2" :disabled="isViewMode || item.choose_switch !== 1" @change="handleFormChange">{{ item.check_switch === 1 ? '白名单验证' : '' }}</el-checkbox>
            </div>
          </div>
        </div>
      </el-form-item>
      <el-form-item label="登录白名单">
        <div class="login-whitelist-switch">
          <el-radio-group v-model="loginWhitelistSwitch" :disabled="isViewMode" @change="handleLoginWhitelistSwitchChange">
            <el-radio label="1">开启</el-radio>
            <el-radio label="2">关闭</el-radio>
          </el-radio-group>
        </div>
      </el-form-item>
      <el-form-item label="按钮样式/表单提交">
        <div class="upload-container">
          <div class="upload-row">
            <!-- 图片展示 -->
            <div v-if="localLoginSetting.login_btn_pic" class="image-preview-list">
              <div class="image-preview-item">
                <img :src="String(localLoginSetting.login_btn_pic).trim()" alt="按钮样式" class="preview-image btn-preview-image">
                <div class="button-container">
                  <el-button v-if="!isViewMode" type="danger" size="small" @click="removeShareImage('login_btn_pic')">删除</el-button>
                </div>
              </div>
            </div>
            <!-- 上传占位符 -->
            <div v-else class="upload-placeholder btn-upload-placeholder">
              <div class="placeholder-icon">!</div>
            </div>
            <!-- 上传按钮，仅在非查看模式时显示且未上传图片时显示 -->
            <div v-if="!isViewMode && !localLoginSetting.login_btn_pic" class="upload-button btn-upload-button">
              <el-button type="primary" plain :disabled="isViewMode" @click="uploadShareImage('login_btn_pic')">+</el-button>
            </div>
          </div>
          <div class="upload-tip">建议图片尺寸：178*48</div>
        </div>
      </el-form-item>
    </el-form>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch, computed } from 'vue';
import { ElMessage } from 'element-plus';
import baseService from "@/service/baseService";

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
        only_switch: 0,
        params_name: 'id_card_no',
        check_switch: 2,
        choose_switch: 2,
        required_switch: 2
      },
      {
        form_name: '工号',
        form_type: 'numeric_word',
        form_type_text: '数字编号',
        only_switch: 0,
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
    const loginWhitelistSwitch = ref('2'); // 默认关闭登录白名单
    const localLoginSetting = ref({ login_btn_pic: '' }); // 登录设置

    // 处理登录表单变化
    const handleFormChange = () => {
      // 确保 only_switch 正确设置（唯一ID）
      formItems.value.forEach(item => {
        // 只有当字段被勾选（choose_switch === 1）且是唯一ID时，only_switch 才为 1
        item.only_switch = item.choose_switch === 1 && item.params_name === uniqueId.value ? 1 : 0;
      });
      
      // 构建登录表单数据
      const loginFormData = formItems.value.map(item => ({
        form_name: item.form_name,
        form_type: item.form_type,
        only_switch: item.only_switch,
        params_name: item.params_name,
        check_switch: item.check_switch,
        choose_switch: item.choose_switch,
        required_switch: item.required_switch
      }));
      
      emit('update:form', {
        ...props.form,
        login_switch: loginSwitch.value,
        login_whitelist_switch: loginWhitelistSwitch.value,
        login_form: JSON.stringify(loginFormData),
        login_setting: JSON.stringify(localLoginSetting.value)
      });
    };

    // 处理登录开关变化
    const handleLoginSwitchChange = () => {
      handleFormChange();
    };

    // 处理登录白名单开关变化
    const handleLoginWhitelistSwitchChange = () => {
      handleFormChange();
    };

    // 上传登录按钮图片
    const uploadShareImage = async (type: string) => {
      // 创建文件输入元素
      const input = document.createElement('input');
      input.type = 'file';
      input.accept = 'image/*';
      
      // 监听文件选择事件
      input.onchange = async (e) => {
        const target = e.target as HTMLInputElement;
        if (target.files && target.files[0]) {
          const file = target.files[0];
          
          // 文件大小和类型验证
          if (file.size > 5 * 1024 * 1024) { // 限制5MB
            ElMessage.error('图片大小不能超过5MB');
            return;
          }
          
          // 创建FormData对象
          const formData = new FormData();
          formData.append('file', file);
          
          try {
            // 调用上传接口
            const uploadUrl = `${import.meta.env.VITE_APP_API}/common/upload/image`;
            const res: any = await baseService.post(uploadUrl, formData);
            if (res.code === '00000' && res.data?.url) {
              // 更新本地状态
              if (type === 'login_btn_pic') {
                localLoginSetting.value.login_btn_pic = res.data.url;
                handleFormChange();
                ElMessage.success('图片上传成功');
              }
            } else {
              ElMessage.error('上传失败：' + (res.msg || '未知错误'));
            }
          } catch (error) {
            console.error('上传失败:', error);
            ElMessage.error('上传失败，请重试');
          }
        }
      };
      
      // 触发文件选择
      input.click();
    };

    // 删除登录按钮图片
    const removeShareImage = (type: string) => {
      if (type === 'login_btn_pic') {
        localLoginSetting.value.login_btn_pic = '';
        handleFormChange();
      }
    };

    // 从 props.form 中解析数据
    watch(() => props.form, (newVal) => {
      if (newVal) {
        // 解析登录开关
        if (newVal.login_switch) {
          loginSwitch.value = newVal.login_switch.toString();
        }
        
        // 解析登录白名单开关
        if (newVal.login_whitelist_switch) {
          loginWhitelistSwitch.value = newVal.login_whitelist_switch.toString();
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
            // 解析完成后，更新 only_switch 字段，确保唯一ID标记正确
            formItems.value.forEach(item => {
              item.only_switch = item.choose_switch === 1 && item.params_name === uniqueId.value ? 1 : 0;
            });
            // 触发 Vue 的响应式更新
            formItems.value = [...formItems.value];
          } catch (error) {
            console.error('解析登录表单数据失败:', error);
          }
        }
        
        // 解析登录设置
        if (newVal.login_setting) {
          try {
            localLoginSetting.value = JSON.parse(newVal.login_setting);
          } catch (error) {
            console.error('解析登录设置失败:', error);
          }
        }
      }
    }, { immediate: true, deep: true });

    // 从 props.uiConfig 中解析登录设置
    watch(() => props.uiConfig, (newVal) => {
      if (newVal && newVal.login_setting) {
        try {
          localLoginSetting.value = JSON.parse(newVal.login_setting);
        } catch (error) {
          console.error('解析登录设置失败:', error);
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
        handleFormChange();
      }
    });

    // 处理全选
    const handleSelectAll = (value: boolean) => {
      formItems.value.forEach(item => {
        item.choose_switch = value ? 1 : 2;
      });
      handleFormChange();
    };

    return {
      formItems,
      uniqueId,
      loginSwitch,
      loginWhitelistSwitch,
      localLoginSetting,
      selectAll,
      handleSelectAll,
      handleLoginSwitchChange,
      handleLoginWhitelistSwitchChange,
      handleFormChange,
      uploadShareImage,
      removeShareImage
    };
  }
});
</script>

<style scoped>
.login-config {
  padding: 20px 0;
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

/* 按钮样式预览图片尺寸 */
.btn-preview-image {
  width: 178px;
  height: 48px;
  object-fit: cover;
}

/* 图片预览项布局 */
.image-preview-item {
  display: flex;
  flex-direction: column;
  align-items: center;
}

/* 按钮容器样式 */
.button-container {
  margin-top: 0;
  display: flex;
  justify-content: center;
}

/* 删除按钮样式 */
.button-container .el-button {
  width: 178px;
}
</style>