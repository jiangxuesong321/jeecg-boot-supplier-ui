<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="8" >
            <a-form-model-item label="业务类型" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="bussinessType">
              <a-input v-model="model.bussinessType" placeholder="请输入业务类型"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="操作人ID" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="operatorId">
              <a-input v-model="model.operatorId" placeholder="请输入操作人ID"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="操作人名" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="operatorName">
              <a-input v-model="model.operatorName" placeholder="请输入操作人名"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="操作企业" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="operatorCompany">
              <a-input v-model="model.operatorCompany" placeholder="请输入操作企业"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="操作意见" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="operatorComment">
              <a-input v-model="model.operatorComment" placeholder="请输入操作意见"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="操作类型" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="operatorType">
              <a-input v-model="model.operatorType" placeholder="请输入操作类型"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="操作时间" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="opreatorTime">
              <j-date placeholder="请选择操作时间" v-model="model.opreatorTime"  style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="排序" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="sort">
              <a-input-number v-model="model.sort" placeholder="请输入排序" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="租户ID" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="tenantId">
              <a-input v-model="model.tenantId" placeholder="请输入租户ID"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="删除标志位" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="delFlag">
              <a-input v-model="model.delFlag" placeholder="请输入删除标志位"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="创建人" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="createUser">
              <a-input v-model="model.createUser" placeholder="请输入创建人"  ></a-input>
            </a-form-model-item>
          </a-col>
         <a-col :span="8" >
            <a-form-model-item label="更新人" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="updateUser">
              <a-input v-model="model.updateUser" placeholder="请输入更新人"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="业务单据ID" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="bussinessBillId">
              <a-input v-model="model.bussinessBillId" placeholder="请输入业务单据ID"  ></a-input>
            </a-form-model-item>
          </a-col>
        </a-row>
      </a-form-model>
    </j-form-container>
  </a-spin>
</template>

<script>

  import { httpAction, getAction } from '@/api/manage'
  import { validateDuplicateValue } from '@/utils/util'

  export default {
    name: 'OperateRecordForm',
    components: {
    },
    props: {
      //表单禁用
      disabled: {
        type: Boolean,
        default: false,
        required: false
      }
    },
    data () {
      return {
        model:{
         },
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        confirmLoading: false,
        validatorRules: {
        },
        url: {
          add: "/srm/operateRecord/add",
          edit: "/srm/operateRecord/edit",
          queryById: "/srm/operateRecord/queryById"
        }
      }
    },
    computed: {
      formDisabled(){
        return this.disabled
      },
    },
    created () {
       //备份model原始值
      this.modelDefault = JSON.parse(JSON.stringify(this.model));
    },
    methods: {
      add () {
        this.edit(this.modelDefault);
      },
      edit (record) {
        this.model = Object.assign({}, record);
        this.visible = true;
      },
      submitForm () {
        const that = this;
        // 触发表单验证
        this.$refs.form.validate(valid => {
          if (valid) {
            that.confirmLoading = true;
            let httpurl = '';
            let method = '';
            if(!this.model.id){
              httpurl+=this.url.add;
              method = 'post';
            }else{
              httpurl+=this.url.edit;
               method = 'put';
            }
            httpAction(httpurl,this.model,method).then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
              }else{
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
            })
          }
         
        })
      },
    }
  }
</script>