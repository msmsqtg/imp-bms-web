<template>
  <div class="basic-config">
    <h3>基本信息</h3>
    <el-form :model="localForm" label-width="120px">
      <el-form-item label="活动名称" required>
        <el-input v-model="localForm.name" placeholder="请输入活动名称" :disabled="isViewMode" @input="handleFormChange"></el-input>
      </el-form-item>
      <el-form-item label="活动标题" required>
        <el-input v-model="localForm.title" placeholder="请输入活动标题" :disabled="isViewMode" @input="handleFormChange"></el-input>
      </el-form-item>
      <el-form-item label="活动时间" required>
        <el-date-picker
          v-model="localActivityTimeRange"
          type="daterange"
          range-separator="至"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          format="YYYY-MM-DD HH:mm:ss"
          value-format="YYYY-MM-DD HH:mm:ss"
          style="width: 100%"
          @change="handleTimeChange"
          :disabled="isViewMode"
        ></el-date-picker>
      </el-form-item>

      <el-form-item label="载体类型" required>
        <el-radio-group v-model="localForm.carrier_type" :disabled="isViewMode" @change="handleFormChange">
          <el-radio label="1">APP</el-radio>
          <el-radio label="2">小程序</el-radio>
          <el-radio label="3">公众号</el-radio>
        </el-radio-group>
      </el-form-item>

      <el-form-item label="主题背景图">
        <div class="upload-container">
          <div class="upload-row">
            <!-- 图片展示 -->
            <div v-if="localForm.home_bg_pic && localForm.home_bg_pic.length > 0" class="image-preview-list">
              <div v-for="(image, index) in localForm.home_bg_pic" :key="index" class="image-preview-item">
                <img :src="String(image).trim()" alt="主题背景图" class="preview-image">
                <el-button v-if="!isViewMode" type="danger" size="small" @click="removeImage(Number(index))">删除</el-button>
              </div>
            </div>
            <!-- 上传占位符 -->
            <div v-else class="upload-placeholder">
              <div class="placeholder-icon">!</div>
            </div>
            <!-- 上传按钮，仅在非查看模式且背景图数量小于5张时显示 -->
            <div v-if="!isViewMode && (!localForm.home_bg_pic || localForm.home_bg_pic.length < 5)" class="upload-button">
              <el-button type="primary" plain :disabled="isViewMode" @click="uploadImage">+</el-button>
            </div>
          </div>
          <div class="upload-tip">建议尺寸：宽750px 高750px，格式为jpg/png/gif，最多上传5张</div>
        </div>
      </el-form-item>
      <h3>关联活动设置</h3>
      <el-form-item label="是否有关联活动">
        <el-switch v-model="localForm.has_related_activity" :disabled="isViewMode" @change="handleFormChange"></el-switch>
      </el-form-item>
      
      <el-form-item label="选择关联活动" required v-if="localForm.has_related_activity">
        <el-select v-model="localForm.related_activity_1" placeholder="选择关联活动" :disabled="isViewMode" @change="handleFormChange" style="width: 200px; margin-right: 10px;">
          <el-option label="新健步走活动" value="1"></el-option>
          <el-option label="0731健步走活动1" value="2"></el-option>
        </el-select>
        <el-select v-model="localForm.related_activity_2" placeholder="选择关联活动" :disabled="isViewMode" @change="handleFormChange" style="width: 200px;">
          <el-option label="新健步走活动" value="1"></el-option>
          <el-option label="0731健步走活动1" value="2"></el-option>
        </el-select>
      </el-form-item>
      
      <el-form-item label="选择关联机构" required v-if="localForm.has_related_activity">
        <el-select v-model="localForm.related_organization" placeholder="选择关联机构" :disabled="isViewMode" @change="handleFormChange" style="width: 200px;">
          <el-option label="鼎翰文化" value="1"></el-option>
        </el-select>
      </el-form-item>
      <h3>抽奖次数设置</h3>
      <el-form-item label="启用代理人次数限制">
        <el-switch v-model="localForm.whitelist_qty_switch" :disabled="isViewMode" @change="handleFormChange" :active-value="1" :inactive-value="2"></el-switch>
      </el-form-item>
      
      <el-form-item label="客户参与活动次数超限提示语" required v-if="localForm.whitelist_qty_switch === 1">
        <el-input
          v-model="localForm.whitelist_qty_switch_msg"
          type="textarea"
          :rows="3"
          placeholder="客户参与活动次数超限提示语"
          :disabled="isViewMode"
          @input="handleFormChange"
          maxlength="20"
          show-word-limit
        ></el-input>
      </el-form-item>
      
      <h3>代理人邀约码设置</h3>
      <el-form-item label="启用代理人唯一邀约码限制">
        <el-switch v-model="localForm.invitation_code_switch" :disabled="isViewMode" @change="handleFormChange" :active-value="1" :inactive-value="2"></el-switch>
      </el-form-item>
      
      <el-form-item label="限制次数" required v-if="localForm.invitation_code_switch === 1">
        <el-input-number v-model="localForm.invitation_code_num" :min="1" :max="99" :disabled="isViewMode" @change="handleFormChange"></el-input-number>
      </el-form-item>
      
      <el-form-item label="客户邀约码被使用提示语" required v-if="localForm.invitation_code_switch === 1">
        <el-input
          v-model="localForm.invitation_code_switch_msg"
          type="textarea"
          :rows="3"
          placeholder="客户邀约码被使用提示语"
          :disabled="isViewMode"
          @input="handleFormChange"
          maxlength="20"
          show-word-limit
        ></el-input>
      </el-form-item>
      <h3>内容设置</h3>
      <el-form-item label="登录开关">
        <el-radio-group v-model="localForm.login_switch" :disabled="isViewMode" @change="handleFormChange">
          <el-radio label="1">开启</el-radio>
          <el-radio label="2">关闭</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="资料提交开关">
        <el-radio-group v-model="localForm.data_switch" :disabled="isViewMode" @change="handleFormChange">
          <el-radio label="1">开启</el-radio>
          <el-radio label="2">关闭</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="我的客户开关">
        <el-radio-group v-model="localForm.customer_switch" :disabled="isViewMode" @change="handleFormChange">
          <el-radio label="1">开启</el-radio>
          <el-radio label="2">关闭</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="签到开关">
        <el-radio-group v-model="localForm.sign_in_status" :disabled="isViewMode" @change="handleFormChange">
          <el-radio label="1">开启</el-radio>
          <el-radio label="0">关闭</el-radio>
        </el-radio-group>
      </el-form-item>
      <h3>分享模式设置</h3>
      <el-form-item label="分享模式">
        <el-radio-group v-model="localForm.share_mode" :disabled="isViewMode" @change="handleFormChange" class="share-mode-group">
          <el-radio label="1">邀约海报</el-radio>
          <el-radio label="2">邀约海报加一键分享</el-radio>
          <el-radio label="3">邀约二维码</el-radio>
        </el-radio-group>
      </el-form-item>
      
      <!-- 微信分享设置（模式2需要） -->
      <div v-if="localForm.share_mode === '2'">
        <el-form-item label="微信分享标题" required>
          <el-input v-model="localShareBgSetting.wx_share_title" placeholder="微信分享标题" :disabled="isViewMode" @input="handleShareBgSettingChange" maxlength="10" show-word-limit></el-input>
        </el-form-item>
        
        <el-form-item label="微信分享内容简介" required>
          <el-input v-model="localShareBgSetting.wx_share_desc" placeholder="微信分享内容简介" :disabled="isViewMode" @input="handleShareBgSettingChange" maxlength="30" show-word-limit></el-input>
        </el-form-item>
        
        <el-form-item label="微信分享缩略图片">
          <div class="upload-container">
            <div class="upload-row">
              <!-- 图片展示 -->
              <div v-if="localShareBgSetting.wx_share_pic" class="image-preview-list">
                <div class="image-preview-item">
                  <img :src="String(localShareBgSetting.wx_share_pic).trim()" alt="微信分享缩略图片" class="preview-image">
                  <el-button v-if="!isViewMode" type="danger" size="small" @click="removeShareImage('wx_share_pic')">删除</el-button>
                </div>
              </div>
              <!-- 上传占位符 -->
              <div v-else class="upload-placeholder">
                <div class="placeholder-icon">!</div>
              </div>
              <!-- 上传按钮，仅在非查看模式时显示 -->
              <div v-if="!isViewMode" class="upload-button">
                <el-button type="primary" plain :disabled="isViewMode" @click="uploadShareImage('wx_share_pic')">+</el-button>
              </div>
            </div>
            <div class="upload-tip">建议图片尺寸：640*962</div>
          </div>
        </el-form-item>
      </div>
      
      <!-- 邀约码有效期（仅模式3需要） -->
      <el-form-item label="邀约码有效期(秒)" required v-if="localForm.share_mode === '3'">
        <el-input-number v-model="localShareBgSetting.invitation_code_expire" :min="0" :max="86400" :disabled="isViewMode" @change="handleShareBgSettingChange"></el-input-number>
      </el-form-item>
      
      <!-- 通用分享海报设置 -->
      <el-form-item label="分享海报背景图">
        <div class="upload-container">
          <div class="upload-row">
            <!-- 图片展示 -->
            <div v-if="localShareBgSetting.share_bg_pic" class="image-preview-list">
              <div class="image-preview-item">
                <img :src="String(localShareBgSetting.share_bg_pic).trim()" alt="分享海报背景图" class="preview-image">
                <el-button v-if="!isViewMode" type="danger" size="small" @click="removeShareImage('share_bg_pic')">删除</el-button>
              </div>
            </div>
            <!-- 上传占位符 -->
            <div v-else class="upload-placeholder">
              <div class="placeholder-icon">!</div>
            </div>
            <!-- 上传按钮，仅在非查看模式时显示 -->
            <div v-if="!isViewMode" class="upload-button">
              <el-button type="primary" plain :disabled="isViewMode" @click="uploadShareImage('share_bg_pic')">+</el-button>
            </div>
          </div>
          <div class="upload-tip">建议图片尺寸：640*962</div>
        </div>
      </el-form-item>
      
      <el-form-item label="按钮样式/生成邀约海报">
        <div class="upload-container">
          <div class="upload-row">
            <!-- 图片展示 -->
            <div v-if="localShareBgSetting.share_btn_pic" class="image-preview-list">
              <div class="image-preview-item">
                <img :src="String(localShareBgSetting.share_btn_pic).trim()" alt="按钮样式" class="preview-image">
                <el-button v-if="!isViewMode" type="danger" size="small" @click="removeShareImage('share_btn_pic')">删除</el-button>
              </div>
            </div>
            <!-- 上传占位符 -->
            <div v-else class="upload-placeholder">
              <div class="placeholder-icon">!</div>
            </div>
            <!-- 上传按钮，仅在非查看模式时显示 -->
            <div v-if="!isViewMode" class="upload-button">
              <el-button type="primary" plain :disabled="isViewMode" @click="uploadShareImage('share_btn_pic')">+</el-button>
            </div>
          </div>
          <div class="upload-tip">建议图片尺寸：560*96</div>
        </div>
      </el-form-item>
      
      <el-form-item label="邀约代理人海报样式配置">
        <div class="style-config-container">
          <div class="style-option-group">
            <div class="style-option">
              <el-radio-group v-model="localShareBgSetting.share_style" :disabled="isViewMode" @change="handleShareBgSettingChange">
                <div class="style-item">
                  <el-radio label="1">样式一(带姓名)</el-radio>
                  <div class="style-preview-item">
                    <img src="https://test-integral-erp-web.sqqmall.com/img/activity_mode1.c61b3059.png" alt="样式一" class="style-preview">
                  </div>
                </div>
                <div class="style-item">
                  <el-radio label="2">样式二</el-radio>
                  <div class="style-preview-item">
                    <img src="https://test-integral-erp-web.sqqmall.com/img/activity_mode2.b00b298a.png" alt="样式二" class="style-preview">
                  </div>
                </div>
              </el-radio-group>
            </div>
          </div>
        </div>
      </el-form-item>
    </el-form>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch } from 'vue';
import { ElMessage } from 'element-plus';
import baseService from "@/service/baseService";

export default defineComponent({
  name: "BasicConfig",
  props: {
    form: {
      type: Object,
      default: () => ({
        id: '',
        store_id: 0,
        name: '',
        title: '',
        activity_no: '',
        activity_type: '',
        activity_id: '',
        start_time: '',
        end_time: '',
        carrier_type: '3',
        login_switch: '1',
        login_whitelist_switch: '2',
        data_switch: '2',
        c_data_switch: '2',
        customer_switch: '2',
        sign_in_status: '0',
        login_form: '',
        data_form: '',
        c_data_form: '',
        share_mode: '1',
        is_del: 0,
        tenant_id: 1,
        home_bg_pic: [] as any[],
        has_related_activity: false,
        related_activity_1: '',
        related_activity_2: '',
        related_organization: '',
        whitelist_qty_switch: 2,
        whitelist_qty_switch_msg: '',
        invitation_code_switch: 2,
        invitation_code_switch_msg: '',
        invitation_code_num: 1,
        share_bg_setting: '{"share_style": 1, "share_bg_pic": "", "wx_share_pic": "", "share_btn_pic": "", "share_id_type": 1, "wx_share_desc": "", "wx_share_title": ""}'
      })
    },
    isViewMode: {
      type: Boolean,
      default: false
    }
  },
  emits: ['update:form', 'save-success'],
  setup(props, { emit }) {
    const localForm = ref({ ...props.form });
    const localActivityTimeRange = ref<string[]>([]);
    const localShareBgSetting = ref({
      share_style: 1,
      share_bg_pic: '',
      wx_share_pic: '',
      share_btn_pic: '',
      share_id_type: 1,
      wx_share_desc: '',
      wx_share_title: '',
      invitation_code_expire: 0
    });

    // 初始化时间范围
    if (props.form.start_time && props.form.end_time) {
      localActivityTimeRange.value = [props.form.start_time, props.form.end_time];
    }

    // 初始化时确保关联活动设置字段有正确的值
    if (localForm.value.has_related_activity === undefined) {
      localForm.value.has_related_activity = false;
    }
    if (localForm.value.related_activity_1 === undefined) {
      localForm.value.related_activity_1 = '';
    }
    if (localForm.value.related_activity_2 === undefined) {
      localForm.value.related_activity_2 = '';
    }
    if (localForm.value.related_organization === undefined) {
      localForm.value.related_organization = '';
    }
    // 初始化时确保抽奖次数设置字段有正确的值
    if (localForm.value.whitelist_qty_switch === undefined) {
      localForm.value.whitelist_qty_switch = 2;
    }
    if (localForm.value.whitelist_qty_switch_msg === undefined) {
      localForm.value.whitelist_qty_switch_msg = '';
    }
    // 初始化时确保代理人邀约码设置字段有正确的值
    if (localForm.value.invitation_code_switch === undefined) {
      localForm.value.invitation_code_switch = 2;
    }
    if (localForm.value.invitation_code_switch_msg === undefined) {
      localForm.value.invitation_code_switch_msg = '';
    }
    if (localForm.value.invitation_code_num === undefined) {
      localForm.value.invitation_code_num = 1;
    }
    // 初始化时确保分享设置字段有正确的值
    if (localForm.value.share_mode === undefined) {
      localForm.value.share_mode = '1';
    }
    if (localForm.value.share_bg_setting === undefined) {
      localForm.value.share_bg_setting = '{"share_style": 1, "share_bg_pic": "", "wx_share_pic": "", "share_btn_pic": "", "share_id_type": 1, "wx_share_desc": "", "wx_share_title": ""}';
    }
    // 解析share_bg_setting
    try {
      const parsedSetting = JSON.parse(localForm.value.share_bg_setting);
      localShareBgSetting.value = {
        share_style: 1,
        share_bg_pic: '',
        wx_share_pic: '',
        share_btn_pic: '',
        share_id_type: 1,
        wx_share_desc: '',
        wx_share_title: '',
        invitation_code_expire: 0,
        ...parsedSetting
      };
    } catch (error) {
      console.error('解析share_bg_setting失败:', error);
    }

    // 监听外部 form 变化
    watch(
      () => props.form,
      (newForm) => {
        localForm.value = { ...newForm };
        // 确保 carrier_type 是字符串类型
        if (newForm.carrier_type !== undefined) {
          localForm.value.carrier_type = String(newForm.carrier_type);
        }
        // 确保 home_bg_pic 是数组类型
        if (newForm.home_bg_pic) {
          localForm.value.home_bg_pic = Array.isArray(newForm.home_bg_pic) ? newForm.home_bg_pic : [];
        } else {
          localForm.value.home_bg_pic = [];
        }
        // 确保 has_related_activity 是布尔类型
        if (newForm.has_related_activity !== undefined) {
          localForm.value.has_related_activity = newForm.has_related_activity;
        }
        // 确保 related_activity_1 是字符串类型
        if (newForm.related_activity_1 !== undefined) {
          localForm.value.related_activity_1 = String(newForm.related_activity_1);
        }
        // 确保 related_activity_2 是字符串类型
        if (newForm.related_activity_2 !== undefined) {
          localForm.value.related_activity_2 = String(newForm.related_activity_2);
        }
        // 确保 related_organization 是字符串类型
        if (newForm.related_organization !== undefined) {
          localForm.value.related_organization = String(newForm.related_organization);
        }
        // 确保 whitelist_qty_switch 是数字类型
        if (newForm.whitelist_qty_switch !== undefined) {
          localForm.value.whitelist_qty_switch = Number(newForm.whitelist_qty_switch);
        }
        // 确保 whitelist_qty_switch_msg 是字符串类型
        if (newForm.whitelist_qty_switch_msg !== undefined) {
          localForm.value.whitelist_qty_switch_msg = String(newForm.whitelist_qty_switch_msg);
        }
        // 确保 invitation_code_switch 是数字类型
        if (newForm.invitation_code_switch !== undefined) {
          localForm.value.invitation_code_switch = Number(newForm.invitation_code_switch);
        }
        // 确保 invitation_code_switch_msg 是字符串类型
        if (newForm.invitation_code_switch_msg !== undefined) {
          localForm.value.invitation_code_switch_msg = String(newForm.invitation_code_switch_msg);
        }
        // 确保 invitation_code_num 是数字类型
        if (newForm.invitation_code_num !== undefined) {
          localForm.value.invitation_code_num = Number(newForm.invitation_code_num);
        }
        // 确保 share_mode 是字符串类型
        if (newForm.share_mode !== undefined) {
          localForm.value.share_mode = String(newForm.share_mode);
        }
        // 确保 share_bg_setting 是字符串类型并解析
        if (newForm.share_bg_setting !== undefined) {
          localForm.value.share_bg_setting = String(newForm.share_bg_setting);
          try {
            const parsedSetting = JSON.parse(localForm.value.share_bg_setting);
            localShareBgSetting.value = {
              share_style: 1,
              share_bg_pic: '',
              wx_share_pic: '',
              share_btn_pic: '',
              share_id_type: 1,
              wx_share_desc: '',
              wx_share_title: '',
              invitation_code_expire: 0,
              ...parsedSetting
            };
          } catch (error) {
            console.error('解析share_bg_setting失败:', error);
          }
        }
        if (newForm.start_time && newForm.end_time) {
          localActivityTimeRange.value = [newForm.start_time, newForm.end_time];
        }
      },
      { deep: true }
    );

    // 初始化时确保 carrier_type 是字符串类型
    if (props.form.carrier_type !== undefined) {
      localForm.value.carrier_type = String(props.form.carrier_type);
    }
    // 初始化时确保 home_bg_pic 是数组类型
    if (props.form.home_bg_pic) {
      localForm.value.home_bg_pic = Array.isArray(props.form.home_bg_pic) ? props.form.home_bg_pic : [];
    } else {
      localForm.value.home_bg_pic = [];
    }
    // 确保其他字段有正确的值
    if (localForm.value.login_switch === undefined) {
      localForm.value.login_switch = '1';
    }
    if (localForm.value.data_switch === undefined) {
      localForm.value.data_switch = '2';
    }
    if (localForm.value.customer_switch === undefined) {
      localForm.value.customer_switch = '2';
    }
    if (localForm.value.sign_in_status === undefined) {
      localForm.value.sign_in_status = '0';
    }
    if (localForm.value.share_mode === undefined) {
      localForm.value.share_mode = '1';
    }

    const handleTimeChange = (value: any) => {
      localActivityTimeRange.value = value;
      if (value && value.length === 2) {
        localForm.value.start_time = value[0];
        localForm.value.end_time = value[1];
        emit('update:form', { ...localForm.value });
      }
    };

    const handleFormChange = () => {
      emit('update:form', { ...localForm.value });
    };

    // 图片上传函数
    const uploadImage = () => {
      const input = document.createElement('input');
      input.type = 'file';
      input.accept = 'image/*';
      input.onchange = async (e: Event) => {
        const target = e.target as HTMLInputElement;
        const file = target.files?.[0];
        if (file) {
          const formData = new FormData();
          formData.append('file', file);
          try {
            const uploadUrl = `${import.meta.env.VITE_APP_API}/common/upload/image`;
            const res: any = await baseService.post(uploadUrl, formData);
            if (res.code === '00000' && res.data?.url) {
              // 处理主题背景图，支持多张，最多5张
              if (!localForm.value.home_bg_pic) {
                localForm.value.home_bg_pic = [];
              }
              if (localForm.value.home_bg_pic.length < 5) {
                localForm.value.home_bg_pic.push(res.data.url);
              } else {
                ElMessage.warning('最多只能上传5张图片');
                return;
              }
              handleFormChange();
              ElMessage.success('上传成功');
            }
          } catch (error) {
            console.error('上传失败:', error);
            ElMessage.error('上传失败');
          }
        }
      };
      input.click();
    };

    // 删除图片函数
    const removeImage = (index: number) => {
      if (localForm.value.home_bg_pic && localForm.value.home_bg_pic.length > 0) {
        localForm.value.home_bg_pic.splice(index, 1);
        handleFormChange();
        ElMessage.success('删除成功');
      }
    };

    // 分享设置变化处理函数
    const handleShareBgSettingChange = () => {
      localForm.value.share_bg_setting = JSON.stringify(localShareBgSetting.value);
      handleFormChange();
    };

    // 上传分享图片函数
    const uploadShareImage = (field: string) => {
      const input = document.createElement('input');
      input.type = 'file';
      input.accept = 'image/*';
      input.onchange = async (e: Event) => {
        const target = e.target as HTMLInputElement;
        const file = target.files?.[0];
        if (file) {
          const formData = new FormData();
          formData.append('file', file);
          try {
            const uploadUrl = `${import.meta.env.VITE_APP_API}/common/upload/image`;
            const res: any = await baseService.post(uploadUrl, formData);
            if (res.code === '00000' && res.data?.url) {
              // 只对字符串类型的字段赋值
              if (field === 'share_bg_pic' || field === 'wx_share_pic' || field === 'share_btn_pic' || field === 'wx_share_title' || field === 'wx_share_desc') {
                (localShareBgSetting.value as any)[field] = res.data.url;
              }
              handleShareBgSettingChange();
              ElMessage.success('上传成功');
            }
          } catch (error) {
            console.error('上传失败:', error);
            ElMessage.error('上传失败');
          }
        }
      };
      input.click();
    };

    // 删除分享图片函数
    const removeShareImage = (field: string) => {
      // 只对字符串类型的字段赋值
      if (field === 'share_bg_pic' || field === 'wx_share_pic' || field === 'share_btn_pic' || field === 'wx_share_title' || field === 'wx_share_desc') {
        (localShareBgSetting.value as any)[field] = '';
      }
      handleShareBgSettingChange();
      ElMessage.success('删除成功');
    };

    return {
      localForm,
      localActivityTimeRange,
      localShareBgSetting,
      handleTimeChange,
      handleFormChange,
      handleShareBgSettingChange,
      uploadImage,
      removeImage,
      uploadShareImage,
      removeShareImage
    };
  }
});
</script>

<style scoped>
.basic-config {
  padding: 20px 0;
}

/* 修复label多行时被遮挡的问题 */
:deep(.el-form-item) {
  margin-bottom: 20px;
}

:deep(.el-form-item__label) {
  white-space: normal;
  line-height: 1.4;
  height: auto;
  padding-top: 0;
  padding-bottom: 5px;
}

:deep(.el-form-item__content) {
  margin-left: 130px !important;
}

.upload-container {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  flex-wrap: wrap;
}

.upload-container {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  flex-wrap: wrap;
  width: 100%;
}

.upload-row {
  display: flex;
  flex-wrap: wrap;
  align-items: flex-start;
  margin-bottom: 10px;
  width: 100%;
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
  margin-bottom: 10px;
}

.placeholder-icon {
  font-size: 48px;
  color: #409eff;
  font-weight: bold;
}

.upload-button {
  margin-right: 10px;
  margin-bottom: 10px;
  align-self: flex-start;
}

.upload-tip {
  color: #909399;
  font-size: 12px;
  width: 100%;
  margin-top: 5px;
}

.image-preview-list {
  display: flex;
  flex-wrap: wrap;
  margin-right: 10px;
  margin-bottom: 10px;
}

.image-preview-item {
  margin: 0 10px 10px 0;
  position: relative;
}

.preview-image {
  width: 150px;
  height: 150px;
  object-fit: cover;
  border: 1px solid #e4e7ed;
  border-radius: 4px;
}

.image-preview-item .el-button {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  margin: 0;
  border-radius: 0 0 4px 4px;
}

/* 样式选项样式 */
.style-option {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 200px;
  margin: 0 20px 20px 0;
}

/* 分享模式组样式 */
.share-mode-group {
  display: flex;
  flex-wrap: wrap;
  gap: 15px;
}

.share-mode-group .el-radio {
  display: flex;
  align-items: center;
  margin-right: 0;
  margin-bottom: 0;
}

.share-mode-group .el-radio__input {
  margin-right: 5px;
}

/* 确保内容编辑区域不被限制高度 */
.el-form {
  min-height: 100%;
}

/* 确保整个组件高度自适应 */
.basic-config {
  min-height: 100%;
  overflow-y: auto;
  padding: 20px;
}

/* 样式配置容器 */
.style-config-container {
  width: 100%;
}

.style-option-group {
  width: 100%;
}

.style-option {
  width: 100%;
}

/* 确保radio组正确显示 */
:deep(.style-option .el-radio-group) {
  display: flex;
  flex-wrap: wrap;
  gap: 30px;
  width: 100%;
}

.style-item {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  margin-right: 20px;
  margin-bottom: 20px;
}

.style-preview-item {
  margin-top: 10px;
}

.style-preview {
  width: 200px;
  height: auto;
  border: 1px solid #e4e7ed;
  border-radius: 4px;
}

/* 确保radio按钮和文字正确显示 */
:deep(.style-item .el-radio) {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}

/* 调整radio-group布局 */
:deep(.el-radio-group) {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  width: 100%;
}

:deep(.el-radio) {
  margin-right: 20px;
  margin-bottom: 10px;
  display: flex;
  align-items: center;
}

/* 确保radio标签正确显示 */
:deep(.el-radio__input) {
  margin-right: 5px;
  margin-bottom: 0;
  display: inline-block;
}
</style>