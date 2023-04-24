<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <!-- 主表单区域 -->
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="12" >
            <a-form-model-item label="收款月份" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="planMonth">
              <a-input v-model="model.planMonth" placeholder="请输入收款月份" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12" >
            <a-form-model-item label="应付（本币）" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="payAmountCope">
              <a-input-number v-model="model.payAmountCope" placeholder="请输入应收款金额本币" style="width: 100%" />
            </a-form-model-item>
          </a-col>

          <a-col :span="12" >
            <a-form-model-item label="收款状态" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="payStatus">
              <a-input v-model="model.payStatus" placeholder="请输入收款状态" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="12" >
            <a-form-model-item label="收款比例" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="payRate">
              <a-input-number v-model="model.payRate" placeholder="请输入收款比例" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12" >
            <a-form-model-item label="已付（本币）" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="payAmountPaid">
              <a-input-number v-model="model.payAmountPaid" placeholder="请输入实际收款金额本币" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12" >
            <a-form-model-item label="应付（美元）" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="payAmountUsd">
              <a-input-number v-model="model.payAmountUsd" placeholder="请输入应收款金额美元" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12" >
            <a-form-model-item label="应付（日元）" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="payAmountJpy">
              <a-input-number v-model="model.payAmountJpy" placeholder="请输入应收款金额日元" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12" >
            <a-form-model-item label="应付（欧元）" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="payAmountEur">
              <a-input-number v-model="model.payAmountEur" placeholder="请输入应收款金额欧元" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="12" >
            <a-form-model-item label="备注" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="comment">
              <a-input v-model="model.comment" placeholder="请输入备注" ></a-input>
            </a-form-model-item>
          </a-col>

        </a-row>
      </a-form-model>
    </j-form-container>
      <!-- 子表单区域 -->
    <a-tabs v-model="activeKey" @change="handleChangeTabs">
      <a-tab-pane tab="收款计划明细" :key="refKeys[0]" :forceRender="true">
        <j-vxe-table
          keep-source
          :ref="refKeys[0]"
          :loading="purPayPlanDetailTable.loading"
          :columns="purPayPlanDetailTable.columns"
          :dataSource="purPayPlanDetailTable.dataSource"
          :maxHeight="300"
          :disabled="formDisabled"
          :rowNumber="true"
          :rowSelection="true"
          :toolbar="true"
          />
      </a-tab-pane>
    </a-tabs>
  </a-spin>
</template>

<script>

  import { getAction } from '@/api/manage'
  import { JVxeTableModelMixin } from '@/mixins/JVxeTableModelMixin.js'
  import { JVXETypes } from '@/components/jeecg/JVxeTable'
  import { getRefPromise,VALIDATE_FAILED} from '@/components/jeecg/JVxeTable/utils/vxeUtils.js'
  import { validateDuplicateValue } from '@/utils/util'
  import JFormContainer from '@/components/jeecg/JFormContainer'

  export default {
    name: 'PurPayPlanForm',
    mixins: [JVxeTableModelMixin],
    components: {
      JFormContainer,
    },
    data() {
      return {
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        model:{
         },
        // 新增时子表默认添加几行空数据
        addDefaultRowNum: 1,
        validatorRules: {
        },
        refKeys: ['purPayPlanDetail', ],
        tableKeys:['purPayPlanDetail', ],
        activeKey: 'purPayPlanDetail',
        // 收款计划明细
        purPayPlanDetailTable: {
          loading: false,
          dataSource: [],
          columns: [
/*            {
              title: '收款计划ID',
              key: 'payPlanId',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
       */
            {
              title: '收款申请ID',
              key: 'payApplyId',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '排序',
              key: 'sort',
              type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
          ]
        },
        url: {
          add: "/srm/purPayPlan/add",
          edit: "/srm/purPayPlan/edit",
          queryById: "/srm/purPayPlan/queryById",
          purPayPlanDetail: {
            list: '/srm/purPayPlan/queryPurPayPlanDetailByMainId'
          },
        }
      }
    },
    props: {
      //表单禁用
      disabled: {
        type: Boolean,
        default: false,
        required: false
      }
    },
    computed: {
      formDisabled(){
        return this.disabled
      },
    },
    created () {
    },
    methods: {
      addBefore(){
        this.purPayPlanDetailTable.dataSource=[]
      },
      getAllTable() {
        let values = this.tableKeys.map(key => getRefPromise(this, key))
        return Promise.all(values)
      },
      /** 调用完edit()方法之后会自动调用此方法 */
      editAfter() {
        this.$nextTick(() => {
        })
        // 加载子表数据
        if (this.model.id) {
          let params = { id: this.model.id }
          this.requestSubTableData(this.url.purPayPlanDetail.list, params, this.purPayPlanDetailTable)
        }
      },
      //校验所有一对一子表表单
        validateSubForm(allValues){
            return new Promise((resolve,reject)=>{
              Promise.all([
              ]).then(() => {
                resolve(allValues)
              }).catch(e => {
                if (e.error === VALIDATE_FAILED) {
                  // 如果有未通过表单验证的子表，就自动跳转到它所在的tab
                  this.activeKey = e.index == null ? this.activeKey : this.refKeys[e.index]
                } else {
                  console.error(e)
                }
              })
            })
        },
      /** 整理成formData */
      classifyIntoFormData(allValues) {
        let main = Object.assign(this.model, allValues.formValue)
        return {
          ...main, // 展开
          purPayPlanDetailList: allValues.tablesValue[0].tableData,
        }
      },
      validateError(msg){
        this.$message.error(msg)
      },

    }
  }
</script>

<style scoped>
</style>