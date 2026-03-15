<template>
  <div class="group-buy-create">
    <el-card shadow="never" class="rr-view-ctx-card">
      <!-- 标签页导航 -->
      <el-tabs v-model="activeTab">
        <el-tab-pane label="基础设置" name="basic"></el-tab-pane>
        <el-tab-pane label="拼团设置" name="group"></el-tab-pane>
        <el-tab-pane label="开团商品设置" name="product"></el-tab-pane>
        <el-tab-pane label="助力奖品设置" name="prize"></el-tab-pane>
      </el-tabs>

      <!-- 基本信息设置 -->
      <div v-show="activeTab === 'basic'" class="tab-content">
        <BasicSettings
          :form="form"
          :activityTimeRange="activityTimeRange"
          :isViewMode="isViewMode"
          @update:form="form = $event"
          @update:activityTimeRange="activityTimeRange = $event"
        />
        
        <!-- 按钮区域 -->
        <div class="button-area">
          <el-button @click="handleBack">返回列表</el-button>
          <el-button type="primary" @click="handleSubmit">提交</el-button>
        </div>
      </div>

      <!-- 拼团设置 -->
      <div v-show="activeTab === 'group'" class="tab-content">
        <GroupSettings
          :form="form.groupSettings"
          :isViewMode="isViewMode"
          @update:form="form.groupSettings = $event"
        />
        
        <!-- 按钮区域 -->
        <div class="button-area">
          <el-button @click="handleBack">返回列表</el-button>
          <el-button @click="handlePrev">上一步</el-button>
          <el-button type="primary" @click="handleSubmitDetail">提交</el-button>
        </div>
      </div>

      <!-- 开团商品设置 -->
      <div v-show="activeTab === 'product'" class="tab-content">
        <ProductSettings
          :form="form.products"
          :isViewMode="isViewMode"
          @update:form="form.products = $event"
        />
        
        <!-- 按钮区域 -->
        <div class="button-area">
          <el-button @click="handleBack">返回列表</el-button>
          <el-button @click="handlePrev" :disabled="isViewMode">上一步</el-button>
          <el-button type="primary" @click="handleSubmitProducts" :disabled="isViewMode">提交</el-button>
        </div>
      </div>

      <!-- 助力奖品设置 -->
      <div v-show="activeTab === 'prize'" class="tab-content">
        <PrizeSettings
          :form="form.prizeSettings"
          :isViewMode="isViewMode"
          @update:form="form.prizeSettings = $event"
        />
        
        <!-- 按钮区域 -->
        <div class="button-area">
          <el-button @click="handleBack">返回列表</el-button>
          <el-button @click="handlePrev" :disabled="isViewMode">上一步</el-button>
          <el-button type="primary" @click="handleSubmit" :disabled="isViewMode">提交</el-button>
        </div>
      </div>
    </el-card>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import baseService from "@/service/baseService";
import BasicSettings from './components/BasicSettings.vue';
import GroupSettings from './components/GroupSettings.vue';
import ProductSettings from './components/ProductSettings.vue';
import PrizeSettings from './components/PrizeSettings.vue';

interface SubmitData {
  title: string;
  startTime: string;
  endTime: string;
  name: string;
  remark: string;
  style: number;
  key: string;
  secret: string;
  type: number;
}

interface ProductDetail {
  impId: number;
  prizeType: number;
  productType: number;
  name: string;
  price: number;
  teamPrice: number;
  stock: number;
  validityPeriod: number;
  teamNum: number;
  isQuota: number;
  quotaNum: number;
  quoMsg: string;
  thumb: string;
  descrImage: string;
  descr: string;
}

export default defineComponent({
  name: "GroupBuyCreate",
  components: {
    BasicSettings,
    GroupSettings,
    ProductSettings,
    PrizeSettings
  },
  data() {
    return {
      activeTab: 'basic',
      activityTimeRange: [] as string[],
      createdImpId: null as number | null, // 存储创建成功的活动ID
      isViewMode: false, // 是否为查看模式
      form: {
        title: '',
        startTime: '',
        endTime: '',
        name: '',
        remark: '',
        style: '1',
        key: 'wxb6d6b65bd5ed9965',
        secret: '76a825b93a7dd93f2b504658e2a3b460',
        type: 2,
        groupSettings: {
          teamType: '2',
          whitelistType: '1',
          nonDesignateCustomerMsg: '开团失败，您不是当前活动受邀用户！',
          multipleTimesSwitch: '2',
          multipleTimesMsg: '开团失败，每位用户仅可成功开团一次！',
          multipleHelpSwitch: '2',
          multipleHelpMsg: '助力失败，每位用户仅可为除自己外一名用户助力一次！',
          rules: {
            headPic: '',
            mainPic: '',
            buttonPicOn: '',
            buttonPicOff: '',
            bottomOver: '',
            formTitle: '请填写相关信息以完成开团',
            formDesc: '请如实填写信息，以便我们及时与您联系',
            formItems: [
              { type: '姓名', title: '姓名1', required: true },
              { type: '身份证后四位', title: '身份证后四位', required: false }
            ],
            formButtonBg: '',
            helpFormTitle: '请填写相关信息以完成助力',
            helpFormDesc: '请如实填写信息，以便我们及时与您联系',
            helpFormItems: [
              { type: '姓名', title: '姓名2', required: true }
            ],
            checkName: 'false',
            needGroupInfo: 'true',
            needHelpInfo: 'true'
          },
          validityPeriod: '',
          participationNum: '',
          blacklistSwitch: '1',
          blacklistMsg: '',
          teamUpLimitSwitch: '1',
          teamUpLimitMax: '',
          teamUpLimitMsg: '',
          wxOpenidSwitch: '2',
          wxOpenidMsg: '',
          userPhoneSwitch: '2',
          userPhoneMsg: ''
        },
        products: [
          {
            type: 'physical',
            name: '',
            price: '',
            stock: '',
            groupPrice: '',
            groupSize: '',
            overLimit: 'allow',
            limit: '',
            specStock: '',
            detail: ''
          }
        ],
        prizeSettings: {
          enable: 'yes',
          name: '',
          price: '',
          stock: '',
          detail: ''
        }
      }
    };
  },
  created() {
    // 页面初始化逻辑
    this.initPage();
  },
  methods: {
    initPage() {
      // 获取 URL 参数
      const impId = this.$route.query.impId;
      const tab = this.$route.query.tab;
      const mode = this.$route.query.mode;
      
      // 切换到指定的 tab
      if (tab) {
        const tabValue = Array.isArray(tab) ? tab[0] : tab;
        if (typeof tabValue === 'string') {
          this.activeTab = tabValue;
        }
      }
      
      // 设置查看模式
      if (mode === 'view') {
        this.isViewMode = true;
      } else {
        this.isViewMode = false;
      }
      
      console.log('isViewMode:', this.isViewMode);
      console.log('mode:', mode);
      
      // 如果有 impId，获取活动详情
      if (impId) {
        const impIdValue = Array.isArray(impId) ? impId[0] : impId;
        if (typeof impIdValue === 'string') {
          this.getActivityDetail(impIdValue);
        }
      }
    },
    
    // 获取活动详情
    getActivityDetail(impId: string) {
      const userId = 4440;
      const requestUrl = `${import.meta.env.VITE_APP_API}/team/up/main/info`;
      const params = { impId };
      const headers = { createUserId: userId };

      console.log('=== 获取活动详情接口调用开始 ===');
      console.log('请求时间:', new Date().toLocaleString('zh-CN', { hour12: false }));
      console.log('用户ID:', userId);
      console.log('请求URL:', requestUrl);
      console.log('请求参数:', JSON.stringify(params, null, 2));
      console.log('请求头:', JSON.stringify(headers, null, 2));

      const startTime = Date.now();

      baseService.get(requestUrl, params, headers)
        .then((res: any) => {
          const endTime = Date.now();
          const duration = endTime - startTime;

          console.log('=== 获取活动详情接口调用成功 ===');
          console.log('响应时间:', new Date().toLocaleString('zh-CN', { hour12: false }));
          console.log('请求耗时:', duration + 'ms');
          console.log('响应数据:', JSON.stringify(res, null, 2));

          if (res.code === '00000') {
            this.createdImpId = res.data.id;
            this.fillFormData(res.data);
          } else {
            (this as any).$message.error('获取活动详情失败：' + res.msg);
          }
        })
        .catch(error => {
          const endTime = Date.now();
          const duration = endTime - startTime;

          console.log('=== 获取活动详情接口调用失败 ===');
          console.log('响应时间:', new Date().toLocaleString('zh-CN', { hour12: false }));
          console.log('请求耗时:', duration + 'ms');
          console.error('错误信息:', error);
          (this as any).$message.error('获取活动详情失败，请重试');
        });
    },
    
    // 填充表单数据
    fillFormData(data: any) {
      // 填充基础设置
      this.form.title = data.title;
      this.form.name = data.name;
      this.form.remark = data.remark;
      this.form.style = String(data.style);
      this.form.key = data.key;
      this.form.secret = data.secret;
      this.form.type = data.type;
      
      // 填充活动时间
      if (data.startTime && data.endTime) {
        this.activityTimeRange = [data.startTime, data.endTime];
        this.form.startTime = data.startTime;
        this.form.endTime = data.endTime;
      }
      
      console.log('表单数据填充完成');
    },
    
    handleTimeChange(value: any) {
      this.activityTimeRange = value;
      if (value && value.length === 2) {
        this.form.startTime = value[0];
        this.form.endTime = value[1];
      }
    },
    handleBack() {
      this.$router.push('/activity-management/group-buy/index');
    },
    handlePrev() {
      const tabs = ['basic', 'group', 'product', 'prize'];
      const currentIndex = tabs.indexOf(this.activeTab);
      if (currentIndex > 0) {
        this.activeTab = tabs[currentIndex - 1];
      }
    },
    handleSubmit() {
      // 查看模式下禁止提交
      if (this.isViewMode) {
        (this as any).$message.info('查看模式下不能提交数据');
        return;
      }
      
      const submitData: SubmitData & { id?: number } = {
        title: this.form.title,
        startTime: this.form.startTime,
        endTime: this.form.endTime,
        name: this.form.name,
        remark: this.form.remark,
        style: parseInt(this.form.style),
        key: this.form.key,
        secret: this.form.secret,
        type: this.form.type
      };

      // 如果有 createdImpId，添加到提交数据中（编辑模式）
      if (this.createdImpId) {
        submitData.id = this.createdImpId;
      }

      const userId = 4440;
      const requestUrl = `${import.meta.env.VITE_APP_API}/team/up/main/add`;
      const headers = { createUserId: userId };

      console.log('=== 接口调用开始 ===');
      console.log('请求时间:', new Date().toLocaleString('zh-CN', { hour12: false }));
      console.log('用户ID:', userId);
      console.log('请求URL:', requestUrl);
      console.log('请求参数:', JSON.stringify(submitData, null, 2));
      console.log('请求头:', JSON.stringify(headers, null, 2));

      const startTime = Date.now();

      baseService.post(requestUrl, submitData, headers)
        .then((res: any) => {
          const endTime = Date.now();
          const duration = endTime - startTime;

          console.log('=== 接口调用成功 ===');
          console.log('响应时间:', new Date().toLocaleString('zh-CN', { hour12: false }));
          console.log('请求耗时:', duration + 'ms');
          console.log('响应数据:', JSON.stringify(res, null, 2));

          if (res.code === '00000') {
            // 保存创建的活动ID
            this.createdImpId = res.data?.id || res.data;
            const isEditMode = !!this.createdImpId;
            (this as any).$message.success(isEditMode ? '活动编辑成功，正在切换到拼团设置...' : '活动创建成功，正在切换到拼团设置...');
            // 切换到拼团设置 tab
            this.activeTab = 'group';
          } else {
            const isEditMode = !!this.createdImpId;
            (this as any).$message.error(isEditMode ? '活动编辑失败：' + res.msg : '活动创建失败：' + res.msg);
          }
        })
        .catch(error => {
          const endTime = Date.now();
          const duration = endTime - startTime;

          console.log('=== 接口调用失败 ===');
          console.log('响应时间:', new Date().toLocaleString('zh-CN', { hour12: false }));
          console.log('请求耗时:', duration + 'ms');
          console.error('错误信息:', error);
          const isEditMode = !!this.createdImpId;
          (this as any).$message.error(isEditMode ? '活动编辑失败，请重试' : '活动创建失败，请重试');
        });
    },

    // 提交拼团设置
    handleSubmitDetail() {
      // 查看模式下禁止提交
      if (this.isViewMode) {
        (this as any).$message.info('查看模式下不能提交数据');
        return;
      }
      
      if (!this.createdImpId) {
        (this as any).$message.error('请先完成基础设置');
        return;
      }

      // 构建拼团设置数据
      const groupSettingsData = {
        impId: this.createdImpId!,
        teamType: parseInt(this.form.groupSettings.teamType),
        whitelistType: parseInt(this.form.groupSettings.whitelistType),
        nonDesignateCustomerMsg: this.form.groupSettings.nonDesignateCustomerMsg,
        multipleTimesSwitch: parseInt(this.form.groupSettings.multipleTimesSwitch),
        multipleTimesMsg: this.form.groupSettings.multipleTimesMsg,
        multipleHelpSwitch: parseInt(this.form.groupSettings.multipleHelpSwitch),
        multipleHelpMsg: this.form.groupSettings.multipleHelpMsg,
        rules: {
          up: {
            nendInfo: this.form.groupSettings.rules.needGroupInfo === 'true' ? 1 : 0,
            title: this.form.groupSettings.rules.formTitle,
            content: this.form.groupSettings.rules.formDesc,
            formData: []
          },
          help: {
            nendInfo: this.form.groupSettings.rules.needHelpInfo === 'true' ? 1 : 0,
            title: this.form.groupSettings.rules.helpFormTitle,
            content: this.form.groupSettings.rules.helpFormDesc,
            formData: [
              {
                name: "姓名",
                type: "name",
                title: "姓名",
                input_name: "name",
                selected: 1,
                check: 0
              }
            ],
            checkName: this.form.groupSettings.rules.checkName === 'true' ? 1 : 2
          }
        },
        orderType: 0,
        teamUpLimitMsg: null,
        luckyLimitMax: 0,
        wxOpenidSwitch: 0,
        signRules: null,
        validityPeriod: 0,
        teamUpLimitSwitch: 0,
        userPhoneSwitch: 0,
        participationNum: 0,
        blacklistSwitch: 0,
        teamUpLimitMax: 0,
        blacklistMsg: null,
        orderOpenTime: null,
        designateCustomerType: 0,
        wxOpenidMsg: null,
        updateTime: new Date().toLocaleString('zh-CN'),
        prizeDrawMode: 0,
        userPhoneMsg: null,
        createTime: new Date().toLocaleString('zh-CN'),
        deleteTime: null,
        fuCardsSwitch: 0
      };

      const userId = 4440;
      const requestUrl = `${import.meta.env.VITE_APP_API}/team/buy/detail/add`;
      const headers = { createUserId: userId };

      console.log('=== 拼团设置接口调用开始 ===');
      console.log('请求时间:', new Date().toLocaleString('zh-CN', { hour12: false }));
      console.log('用户ID:', userId);
      console.log('请求URL:', requestUrl);
      console.log('请求参数:', JSON.stringify(groupSettingsData, null, 2));
      console.log('请求头:', JSON.stringify(headers, null, 2));

      const startTime = Date.now();

      baseService.post(requestUrl, groupSettingsData, headers)
        .then((res: any) => {
          const endTime = Date.now();
          const duration = endTime - startTime;

          console.log('=== 拼团设置接口调用成功 ===');
          console.log('响应时间:', new Date().toLocaleString('zh-CN', { hour12: false }));
          console.log('请求耗时:', duration + 'ms');
          console.log('响应数据:', JSON.stringify(res, null, 2));

          if (res.code === '00000') {
            (this as any).$message.success('拼团设置保存成功');
            // 切换到下一个 tab
            this.activeTab = 'product';
          } else {
            (this as any).$message.error('拼团设置保存失败：' + res.msg);
          }
        })
        .catch(error => {
          const endTime = Date.now();
          const duration = endTime - startTime;

          console.log('=== 拼团设置接口调用失败 ===');
          console.log('响应时间:', new Date().toLocaleString('zh-CN', { hour12: false }));
          console.log('请求耗时:', duration + 'ms');
          console.error('错误信息:', error);
          (this as any).$message.error('拼团设置保存失败，请重试');
        });
    },

    // 提交商品详情
    handleSubmitProducts() {
      // 查看模式下禁止提交
      if (this.isViewMode) {
        (this as any).$message.info('查看模式下不能提交数据');
        return;
      }
      
      if (!this.createdImpId) {
        (this as any).$message.error('请先完成基础设置');
        return;
      }

      // 构建商品详情数据
      const productsData: ProductDetail[] = this.form.products.map(product => ({
        impId: this.createdImpId!,
        prizeType: 1,
        productType: 1,
        name: product.name,
        price: parseFloat(product.price) || 0,
        teamPrice: parseFloat(product.groupPrice) || 0,
        stock: parseInt(product.stock) || 0,
        validityPeriod: 111, // 默认值，可根据需求调整
        teamNum: parseInt(product.groupSize) || 0,
        isQuota: product.overLimit === 'forbid' ? 1 : 0,
        quotaNum: parseInt(product.limit) || 0,
        quoMsg: '您已达到该商品购买上限！',
        thumb: '', // 需要上传图片后填充
        descrImage: '', // 需要上传图片后填充
        descr: product.detail
      }));

      const userId = 4440;
      const requestUrl = `${import.meta.env.VITE_APP_API}/team/buy/detail/add`;
      const headers = { createUserId: userId };

      console.log('=== 商品详情接口调用开始 ===');
      console.log('请求时间:', new Date().toLocaleString('zh-CN', { hour12: false }));
      console.log('用户ID:', userId);
      console.log('请求URL:', requestUrl);
      console.log('请求参数:', JSON.stringify(productsData, null, 2));
      console.log('请求头:', JSON.stringify(headers, null, 2));

      const startTime = Date.now();

      baseService.post(requestUrl, productsData, headers)
        .then((res: any) => {
          const endTime = Date.now();
          const duration = endTime - startTime;

          console.log('=== 商品详情接口调用成功 ===');
          console.log('响应时间:', new Date().toLocaleString('zh-CN', { hour12: false }));
          console.log('请求耗时:', duration + 'ms');
          console.log('响应数据:', JSON.stringify(res, null, 2));

          if (res.code === '00000') {
            (this as any).$message.success('商品详情保存成功');
            // 切换到下一个 tab
            this.activeTab = 'product';
          } else {
            (this as any).$message.error('商品详情保存失败：' + res.msg);
          }
        })
        .catch(error => {
          const endTime = Date.now();
          const duration = endTime - startTime;

          console.log('=== 商品详情接口调用失败 ===');
          console.log('响应时间:', new Date().toLocaleString('zh-CN', { hour12: false }));
          console.log('请求耗时:', duration + 'ms');
          console.error('错误信息:', error);
          (this as any).$message.error('商品详情保存失败，请重试');
        });
    },

  }
});
</script>

<style scoped>
.group-buy-create {
  padding: 20px;
}

.tab-content {
  margin-top: 20px;
}

.tab-content h3 {
  margin-bottom: 20px;
  font-size: 16px;
  font-weight: bold;
}

.tab-content h4 {
  margin: 20px 0 10px 0;
  font-size: 14px;
  font-weight: bold;
}

.button-area {
  margin-top: 30px;
  display: flex;
  justify-content: flex-end;
}

.button-area el-button {
  margin-left: 10px;
}

.product-actions {
  margin-bottom: 20px;
}

.product-actions el-button {
  margin-right: 10px;
}

.product-item {
  border: 1px solid #e4e7ed;
  padding: 20px;
  margin-bottom: 20px;
  border-radius: 4px;
}

.upload-area {
  display: flex;
  align-items: center;
}

.upload-area el-button {
  margin-right: 20px;
}

.upload-tip {
  color: #909399;
  font-size: 12px;
}
</style>
