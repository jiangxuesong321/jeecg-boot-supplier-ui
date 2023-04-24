<template>
  <a-drawer
    :title="title"
    :width="width"
    placement="right"
    :closable="false"
	:headerStyle="{ textAlign: 'center'}"
    @close="close"
    destroyOnClose
    :visible="visible">
		<div>
      <a-form-model ref="form" :model="model">
        <a-row>
          <a-divider orientation="left" style="color: #00A0E9">
            招标基本信息
          </a-divider>
          <a-col :span="8">
            <a-form-model-item label="招标名称" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
                               prop="biddingName">
              {{model.biddingName}}
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="招标编码" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              {{model.biddingNo}}
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="标的名称" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              {{model.prodName}}
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="标的编码" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              {{model.prodCode}}
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="标的数量" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              {{model.qty}}
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="议价价格" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              {{model.bgPriceTax}}
            </a-form-model-item>
          </a-col>
        </a-row>
        <a-row>
          <a-divider orientation="left" style="color: #00A0E9">
            报价信息
          </a-divider>
          <a-col :span="8">
            <a-form-model-item label="供应商名称" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
              {{$store.getters.userInfo.realname}}
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="报价币种" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
              {{model.currency_dictText}}
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="税率" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              {{model.taxRate}}%
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="贸易方式" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              {{model.tradeType}}
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="报价单价" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              {{model.priceTax}}
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="报价总额" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              {{model.amountTax}}
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="更新单价" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              <a-input-number v-model='model.suppBgPriceTax' style='width: 50%' @change='setValue'></a-input-number>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="更新总价" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              <a-input-number v-model='model.suppBgAmountTax' style='width: 50%' disabled></a-input-number>
            </a-form-model-item>
          </a-col>
        </a-row>
        <a-row>
          <a-col :span="8">
            <a-form-model-item label="附件" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              <j-upload v-model="model.attachment" :trigger-change="true"></j-upload>
            </a-form-model-item>
          </a-col>
        </a-row>

      </a-form-model>
		</div>
		<div class="drawer-footer" style="text-align: center;margin-top:15px;">
			<a-button key="approved" @click="handleOk" type="primary">提交报价</a-button>
			<a-button key="cancel" @click="handleCancel" style="margin-left:10px;" type="primary" ghost>取消</a-button>
		</div>
	</a-drawer>
</template>

<script>

import { billModalMixin } from '../../mixins/billModalMixin'
import { isNullOrEmpty } from '@/utils/util'
import { postAction } from '@api/manage'
export default {
    name: 'BiddingMainModal',
    mixins: [billModalMixin],
    components: {

    },
    data() {
      return {
        model:{},
        title:'议价',
        width: '90%',
        visible: false,
        disableSubmit: false
      }
    },
    methods:{
      setValue(){
        this.model.suppBgAmountTax = Number(this.model.suppBgPriceTax) * Number(this.model.qty);
        this.$forceUpdate();
      },
      edit (record) {
        this.visible=true
        this.model = record;
      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        let that = this;
        let suppBgPriceTax = that.model.suppBgPriceTax;
        if(isNullOrEmpty(suppBgPriceTax)){
          that.$message.error('请输入反馈价格');
          return;
        }
        that.$confirm({
          content: '是否确认提交？',
          onOk: () => {
            let url = "/srm/biddingMain/submitBargin";
            postAction(url,that.model).then(res => {
              if(res.success){
                that.$message.success('提交成功');
                that.handleCancel();
                that.$emit('ok');
              }else{
                that.$message.error('提交失败');
              }
            })
          },
        })
      },
      submitCallback(){
        this.$emit('ok');
        this.visible = false;
      },
      handleCancel () {
        this.close()
      }
    }
  }
</script>

<style scoped>
</style>