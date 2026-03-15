<template>
  <div class="product-settings">
    <!-- 商品操作按钮 -->
    <div class="product-actions">
      <el-button type="primary" @click="addProduct" :disabled="isViewMode">添加商品</el-button>
      <el-button type="danger" @click="deleteProduct" :disabled="isViewMode">删除商品</el-button>
    </div>

    <!-- 商品列表 -->
    <div v-for="(product, index) in localForm" :key="index" class="product-item">
      <h4>商品{{ index + 1 }}</h4>
      <el-form :model="product" label-width="120px">
        <el-form-item label="商品类型">
          <el-radio-group v-model="product.type" :disabled="isViewMode">
            <el-radio label="physical">实物商品</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="商品名称" required>
          <el-input v-model="product.name" placeholder="请输入商品名称" :disabled="isViewMode"></el-input>
        </el-form-item>
        <el-form-item label="商品价格" required>
          <el-input v-model="product.price" placeholder="请输入商品价格" :disabled="isViewMode">
            <template #append>元</template>
          </el-input>
        </el-form-item>
        <el-form-item label="商品库存" required>
          <el-input v-model="product.stock" placeholder="请输入商品库存" :disabled="isViewMode">
            <template #append>件</template>
          </el-input>
        </el-form-item>
        <el-form-item label="拼团价格" required>
          <el-input v-model="product.groupPrice" placeholder="请输入拼团价格" :disabled="isViewMode">
            <template #append>元</template>
          </el-input>
        </el-form-item>
        <el-form-item label="成团人数" required>
          <el-input v-model="product.groupSize" placeholder="请输入成团人数" :disabled="isViewMode">
            <template #append>人</template>
          </el-input>
        </el-form-item>
        <el-form-item label="人数超限">
          <el-radio-group v-model="product.overLimit" :disabled="isViewMode">
            <el-radio label="allow">允许</el-radio>
            <el-radio label="forbid">不允许</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="限购数量" required>
          <el-input v-model="product.limit" placeholder="请输入限购数量" :disabled="isViewMode">
            <template #append>件</template>
          </el-input>
        </el-form-item>
        <el-form-item label="规格库存" required>
          <el-input v-model="product.specStock" placeholder="请输入规格库存" :disabled="isViewMode"></el-input>
        </el-form-item>
        <el-form-item label="商品详情图" required>
          <div class="upload-area">
            <el-button type="primary" plain :disabled="isViewMode">+</el-button>
            <div class="upload-tip">建议尺寸：750*auto，大小不超过2M</div>
          </div>
        </el-form-item>
        <el-form-item label="商品详情页" required>
          <div class="upload-area">
            <el-button type="primary" plain :disabled="isViewMode">+</el-button>
            <div class="upload-tip">建议尺寸：750*auto，大小不超过2M</div>
          </div>
        </el-form-item>
        <el-form-item label="商品详情" required>
          <el-input
            v-model="product.detail"
            type="textarea"
            placeholder="请输入商品详情"
            :rows="10"
            :disabled="isViewMode"
          ></el-input>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType, ref, watch } from 'vue';

export default defineComponent({
  name: "ProductSettings",
  props: {
    form: {
      type: Array as PropType<any[]>,
      required: true
    },
    isViewMode: {
      type: Boolean,
      default: false
    }
  },
  emits: ['update:form'],
  setup(props, { emit }) {
    const localForm = ref([...props.form]);

    // 监听外部 form 变化
    watch(
      () => props.form,
      (newForm) => {
        localForm.value = [...newForm];
      },
      { deep: true }
    );

    const addProduct = () => {
      const newProduct = {
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
      };
      localForm.value.push(newProduct);
      emit('update:form', [...localForm.value]);
    };

    const deleteProduct = () => {
      if (localForm.value.length > 1) {
        localForm.value.pop();
        emit('update:form', [...localForm.value]);
      }
    };

    return {
      localForm,
      addProduct,
      deleteProduct
    };
  }
});
</script>

<style scoped>
.product-settings {
  padding: 20px 0;
}

.product-actions {
  margin-bottom: 20px;
}

.product-item {
  margin-bottom: 30px;
  padding: 20px;
  border: 1px solid #e4e7ed;
  border-radius: 4px;
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