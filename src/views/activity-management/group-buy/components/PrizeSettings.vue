<template>
  <div class="prize-settings">
    <h3>助力奖品设置</h3>
    <el-form :model="localForm" label-width="180px">
      <el-form-item label="助力成功是否发放奖品" required>
        <el-radio-group v-model="localForm.enable" :disabled="isViewMode" @change="handleFormChange">
          <el-radio label="yes">是</el-radio>
          <el-radio label="no">否</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="商品名称" required>
        <el-input v-model="localForm.name" placeholder="请输入商品名称" :disabled="isViewMode" @input="handleFormChange"></el-input>
      </el-form-item>
      <el-form-item label="商品价格" required>
        <el-input v-model="localForm.price" placeholder="请输入商品价格" :disabled="isViewMode" @input="handleFormChange">
          <template #append>元</template>
        </el-input>
      </el-form-item>
      <el-form-item label="商品库存" required>
        <el-input v-model="localForm.stock" placeholder="请输入商品库存" :disabled="isViewMode" @input="handleFormChange"></el-input>
      </el-form-item>
      <el-form-item label="商品详情图" required>
        <div class="upload-area">
          <el-button type="primary" plain :disabled="isViewMode">+</el-button>
          <div class="upload-tip">建议尺寸：750*auto，大小不超过2M</div>
        </div>
      </el-form-item>
      <el-form-item label="商品详情" required>
        <el-input
          v-model="localForm.detail"
          type="textarea"
          placeholder="请输入商品详情"
          :rows="10"
          :disabled="isViewMode"
          @input="handleFormChange"
        ></el-input>
      </el-form-item>
    </el-form>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType, ref, watch } from 'vue';

export default defineComponent({
  name: "PrizeSettings",
  props: {
    form: {
      type: Object,
      required: true
    },
    isViewMode: {
      type: Boolean,
      default: false
    }
  },
  emits: ['update:form'],
  setup(props, { emit }) {
    const localForm = ref({ ...props.form });

    // 监听外部 form 变化
    watch(
      () => props.form,
      (newForm) => {
        localForm.value = { ...newForm };
      },
      { deep: true }
    );

    const handleFormChange = () => {
      emit('update:form', { ...localForm.value });
    };

    return {
      localForm,
      handleFormChange
    };
  }
});
</script>

<style scoped>
.prize-settings {
  padding: 20px 0;
}

.upload-area {
  display: flex;
  align-items: center;
}

.upload-tip {
  margin-left: 10px;
  color: #909399;
  font-size: 12px;
}
</style>