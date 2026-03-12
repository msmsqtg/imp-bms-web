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
        <h3>基本信息设置</h3>
        <el-form :model="form" label-width="120px">
          <el-form-item label="* 活动标题" required>
            <el-input v-model="form.activityTitle" placeholder="请输入活动标题" maxlength="50"></el-input>
          </el-form-item>
          <el-form-item label="* 活动名称" required>
            <el-input v-model="form.activityName" placeholder="请输入活动名称" maxlength="50"></el-input>
          </el-form-item>
          <el-form-item label="* 活动时间" required>
            <el-date-picker
              v-model="form.activityTime"
              type="daterange"
              range-separator="至"
              start-placeholder="开始日期"
              end-placeholder="结束日期"
              format="YYYY-MM-DD HH:mm:ss"
              value-format="YYYY-MM-DD HH:mm:ss"
              style="width: 100%"
            ></el-date-picker>
          </el-form-item>
          <el-form-item label="* 活动规则" required>
            <el-input
              v-model="form.activityRule"
              type="textarea"
              placeholder="请输入活动规则"
              :rows="10"
            ></el-input>
          </el-form-item>
          <el-form-item label="活动载体">
            <el-radio-group v-model="form.activityCarrier">
              <el-radio label="小程序">小程序</el-radio>
              <el-radio label="H5">H5</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="* 小程序APPID" required>
            <el-input v-model="form.appId" placeholder="请输入小程序APPID"></el-input>
          </el-form-item>
          <el-form-item label="* 小程序APPSecret" required>
            <el-input v-model="form.appSecret" placeholder="请输入小程序APPSecret"></el-input>
          </el-form-item>
        </el-form>
        
        <!-- 按钮区域 -->
        <div class="button-area">
          <el-button @click="handleBack">返回列表</el-button>
          <el-button type="primary" @click="handleSubmit">提交</el-button>
        </div>
      </div>
      
      <!-- 拼团设置 -->
      <div v-show="activeTab === 'group'" class="tab-content">
        <h3>拼团规则</h3>
        <el-form :model="form.groupSettings" label-width="180px">
          <el-form-item label="拼团模式">
            <el-radio-group v-model="form.groupSettings.groupMode">
              <el-radio label="all">所有人都可开团</el-radio>
              <el-radio label="whitelist">指定白名单可开团</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="白名单类型">
            <el-radio-group v-model="form.groupSettings.whitelistType">
              <el-radio label="name_phone">姓名+前三后四脱敏手机号</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="* 非指定客群参与提示" required>
            <el-input
              v-model="form.groupSettings.nonWhitelistTip"
              placeholder="开团失败，您不是当前活动受邀用户！"
              maxlength="50"
            ></el-input>
          </el-form-item>
          <el-form-item label="重复开团设置">
            <el-radio-group v-model="form.groupSettings.repeatGroup">
              <el-radio label="limit">限制</el-radio>
              <el-radio label="allow">允许</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="* 重复开团提示" required>
            <el-input
              v-model="form.groupSettings.repeatGroupTip"
              placeholder="开团失败，每位用户仅可成功开团一次！"
              maxlength="50"
            ></el-input>
          </el-form-item>
          <el-form-item label="重复助力设置">
            <el-radio-group v-model="form.groupSettings.repeatHelp">
              <el-radio label="limit">限制</el-radio>
              <el-radio label="allow">允许</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="* 重复助力提示" required>
            <el-input
              v-model="form.groupSettings.repeatHelpTip"
              placeholder="助力失败，每位用户仅可为除自己外一名用户助力一次！"
              maxlength="50"
            ></el-input>
          </el-form-item>
        </el-form>
        
        <h3 style="margin-top: 30px;">参与规则</h3>
        <el-form :model="form.groupSettings" label-width="180px">
          <el-form-item label="开团用户是否需要提交资料">
            <el-radio-group v-model="form.groupSettings.needGroupInfo">
              <el-radio label="yes">是</el-radio>
              <el-radio label="no">否</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="* 表单提交文案" required>
            <el-input
              v-model="form.groupSettings.groupFormTip"
              placeholder="请填写相关信息以完成开团"
            ></el-input>
          </el-form-item>
          <el-form-item label="表单说明文案">
            <el-input
              v-model="form.groupSettings.groupFormDesc"
              placeholder="请如实填写信息，以便我们及时与您联系"
            ></el-input>
          </el-form-item>
          <el-form-item label="表单顶项配置">
            <el-table :data="form.groupSettings.groupFormItems" style="width: 100%">
              <el-table-column prop="selected" label="选择" width="80">
                <template #default="scope">
                  <el-checkbox v-model="scope.row.selected"></el-checkbox>
                </template>
              </el-table-column>
              <el-table-column prop="type" label="表单类型"></el-table-column>
              <el-table-column prop="title" label="标题文案">
                <template #default="scope">
                  <el-input v-model="scope.row.title"></el-input>
                </template>
              </el-table-column>
              <el-table-column prop="required" label="是否必填" width="100">
                <template #default="scope">
                  <el-checkbox v-model="scope.row.required"></el-checkbox>
                </template>
              </el-table-column>
            </el-table>
          </el-form-item>
          
          <el-form-item label="助力用户是否需要提交资料">
            <el-radio-group v-model="form.groupSettings.needHelpInfo">
              <el-radio label="yes">是</el-radio>
              <el-radio label="no">否</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="* 助力表单提交文案" required>
            <el-input
              v-model="form.groupSettings.helpFormTip"
              placeholder="请填写相关信息以完成助力"
            ></el-input>
          </el-form-item>
          <el-form-item label="助力表单说明文案">
            <el-input
              v-model="form.groupSettings.helpFormDesc"
              placeholder="请如实填写信息，以便我们及时与您联系"
            ></el-input>
          </el-form-item>
          <el-form-item label="* 是否校验姓名" required>
            <el-radio-group v-model="form.groupSettings.checkName">
              <el-radio label="yes">是</el-radio>
              <el-radio label="no">否</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="助力表单顶项配置">
            <el-table :data="form.groupSettings.helpFormItems" style="width: 100%">
              <el-table-column prop="selected" label="选择" width="80">
                <template #default="scope">
                  <el-checkbox v-model="scope.row.selected"></el-checkbox>
                </template>
              </el-table-column>
              <el-table-column prop="type" label="表单类型"></el-table-column>
              <el-table-column prop="title" label="标题文案">
                <template #default="scope">
                  <el-input v-model="scope.row.title"></el-input>
                </template>
              </el-table-column>
              <el-table-column prop="required" label="是否必填" width="100">
                <template #default="scope">
                  <el-checkbox v-model="scope.row.required"></el-checkbox>
                </template>
              </el-table-column>
            </el-table>
          </el-form-item>
        </el-form>
        
        <!-- 按钮区域 -->
        <div class="button-area">
          <el-button @click="handleBack">返回列表</el-button>
          <el-button @click="handlePrev">上一步</el-button>
          <el-button type="primary" @click="handleSubmit">提交</el-button>
        </div>
      </div>
      
      <!-- 开团商品设置 -->
      <div v-show="activeTab === 'product'" class="tab-content">
        <!-- 商品操作按钮 -->
        <div class="product-actions">
          <el-button type="primary" @click="addProduct">添加商品</el-button>
          <el-button type="danger" @click="deleteProduct">删除商品</el-button>
        </div>
        
        <!-- 商品列表 -->
        <div v-for="(product, index) in form.products" :key="index" class="product-item">
          <h4>商品{{ index + 1 }}</h4>
          <el-form :model="product" label-width="120px">
            <el-form-item label="商品类型">
              <el-radio-group v-model="product.type">
                <el-radio label="physical">实物商品</el-radio>
              </el-radio-group>
            </el-form-item>
            <el-form-item label="* 商品名称" required>
              <el-input v-model="product.name" placeholder="请输入商品名称"></el-input>
            </el-form-item>
            <el-form-item label="* 商品价格" required>
              <el-input v-model="product.price" placeholder="请输入商品价格">
                <template #append>元</template>
              </el-input>
            </el-form-item>
            <el-form-item label="* 商品库存" required>
              <el-input v-model="product.stock" placeholder="请输入商品库存">
                <template #append>件</template>
              </el-input>
            </el-form-item>
            <el-form-item label="* 拼团价格" required>
              <el-input v-model="product.groupPrice" placeholder="请输入拼团价格">
                <template #append>元</template>
              </el-input>
            </el-form-item>
            <el-form-item label="* 成团人数" required>
              <el-input v-model="product.groupSize" placeholder="请输入成团人数">
                <template #append>人</template>
              </el-input>
            </el-form-item>
            <el-form-item label="人数超限">
              <el-radio-group v-model="product.overLimit">
                <el-radio label="allow">允许</el-radio>
                <el-radio label="forbid">不允许</el-radio>
              </el-radio-group>
            </el-form-item>
            <el-form-item label="* 限购数量" required>
              <el-input v-model="product.limit" placeholder="请输入限购数量">
                <template #append>件</template>
              </el-input>
            </el-form-item>
            <el-form-item label="* 规格库存" required>
              <el-input v-model="product.specStock" placeholder="请输入规格库存"></el-input>
            </el-form-item>
            <el-form-item label="* 商品详情图" required>
              <div class="upload-area">
                <el-button type="primary" plain>+</el-button>
                <div class="upload-tip">建议尺寸：750*auto，大小不超过2M</div>
              </div>
            </el-form-item>
            <el-form-item label="* 商品详情页" required>
              <div class="upload-area">
                <el-button type="primary" plain>+</el-button>
                <div class="upload-tip">建议尺寸：750*auto，大小不超过2M</div>
              </div>
            </el-form-item>
            <el-form-item label="* 商品详情" required>
              <el-input
                v-model="product.detail"
                type="textarea"
                placeholder="请输入商品详情"
                :rows="10"
              ></el-input>
            </el-form-item>
          </el-form>
        </div>
        
        <!-- 按钮区域 -->
        <div class="button-area">
          <el-button @click="handleBack">返回列表</el-button>
          <el-button @click="handlePrev">上一步</el-button>
          <el-button type="primary" @click="handleSubmit">提交</el-button>
        </div>
      </div>
      
      <!-- 助力奖品设置 -->
      <div v-show="activeTab === 'prize'" class="tab-content">
        <h3>助力奖品设置</h3>
        <el-form :model="form.prizeSettings" label-width="180px">
          <el-form-item label="* 助力成功是否发放奖品" required>
            <el-radio-group v-model="form.prizeSettings.enable">
              <el-radio label="yes">是</el-radio>
              <el-radio label="no">否</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="* 商品名称" required>
            <el-input v-model="form.prizeSettings.name" placeholder="请输入商品名称"></el-input>
          </el-form-item>
          <el-form-item label="* 商品价格" required>
            <el-input v-model="form.prizeSettings.price" placeholder="请输入商品价格">
              <template #append>元</template>
            </el-input>
          </el-form-item>
          <el-form-item label="* 商品库存" required>
            <el-input v-model="form.prizeSettings.stock" placeholder="请输入商品库存"></el-input>
          </el-form-item>
          <el-form-item label="* 商品详情图" required>
            <div class="upload-area">
              <el-button type="primary" plain>+</el-button>
              <div class="upload-tip">建议尺寸：750*auto，大小不超过2M</div>
            </div>
          </el-form-item>
          <el-form-item label="* 商品详情" required>
            <el-input
              v-model="form.prizeSettings.detail"
              type="textarea"
              placeholder="请输入商品详情"
              :rows="10"
            ></el-input>
          </el-form-item>
        </el-form>
        
        <!-- 按钮区域 -->
        <div class="button-area">
          <el-button @click="handleBack">返回列表</el-button>
          <el-button @click="handlePrev">上一步</el-button>
          <el-button type="primary" @click="handleSubmit">提交</el-button>
        </div>
      </div>
    </el-card>
  </div>
</template>

<script lang="ts">
export default {
  name: "GroupBuyCreate",
  data() {
    return {
      activeTab: 'basic',
      form: {
        activityTitle: '',
        activityName: '',
        activityTime: [],
        activityRule: '',
        activityCarrier: '小程序',
        appId: 'wxb6d6b6b5dd5e9985',
        appSecret: '76a825b93a7dd932b504658e2a3b460',
        groupSettings: {
          groupMode: 'whitelist',
          whitelistType: 'name_phone',
          nonWhitelistTip: '开团失败，您不是当前活动受邀用户！',
          repeatGroup: 'limit',
          repeatGroupTip: '开团失败，每位用户仅可成功开团一次！',
          repeatHelp: 'limit',
          repeatHelpTip: '助力失败，每位用户仅可为除自己外一名用户助力一次！',
          needGroupInfo: 'yes',
          groupFormTip: '请填写相关信息以完成开团',
          groupFormDesc: '请如实填写信息，以便我们及时与您联系',
          groupFormItems: [
            { selected: true, type: '姓名', title: '姓名1', required: true },
            { selected: false, type: '身份证后四位', title: '身份证后四位', required: false }
          ],
          needHelpInfo: 'yes',
          helpFormTip: '请填写相关信息以完成助力',
          helpFormDesc: '请如实填写信息，以便我们及时与您联系',
          checkName: 'no',
          helpFormItems: [
            { selected: true, type: '姓名', title: '姓名2', required: true }
          ]
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
  methods: {
    handleBack() {
      this.$router.push('/activity-management/group-buy/index');
    },
    handlePrev() {
      // 切换到上一个标签页
      const tabs = ['basic', 'group', 'product', 'prize'];
      const currentIndex = tabs.indexOf(this.activeTab);
      if (currentIndex > 0) {
        this.activeTab = tabs[currentIndex - 1];
      }
    },
    handleSubmit() {
      // 提交表单逻辑
      console.log('提交表单', this.form);
    },
    addProduct() {
      // 添加商品
      this.form.products.push({
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
      });
    },
    deleteProduct() {
      // 删除商品
      if (this.form.products.length > 1) {
        this.form.products.pop();
      }
    }
  }
};
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