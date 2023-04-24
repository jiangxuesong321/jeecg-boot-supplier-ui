<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <!-- 主表单区域 -->
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="8" >
            <a-form-model-item label="询价单号" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="inquiryCode">
              <a-input v-model="model.inquiryCode" placeholder="请输入询价单号" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="是否公开" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="isPublic">
              <a-input v-model="model.isPublic" placeholder="请输入是否公开" ></a-input>
            </a-form-model-item>
          </a-col>

          <a-col :span="8" >
            <a-form-model-item label="报价截止日期" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="quotationDeadline">
              <j-date placeholder="请选择报价截止日期" v-model="model.quotationDeadline" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="询价人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="inquirer">
              <a-input v-model="model.inquirer" placeholder="请输入询价人" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="联系方式" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="inquirerTel">
              <a-input v-model="model.inquirerTel" placeholder="请输入联系方式" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="询价日期" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="inquiryDate">
              <j-date placeholder="请选择询价日期" v-model="model.inquiryDate" style="width: 100%" />
            </a-form-model-item>
          </a-col>

          <a-col :span="8" >
            <a-form-model-item label="询价单状态" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="inquiryStatus">
              <a-input v-model="model.inquiryStatus" placeholder="请输入询价单状态" ></a-input>
            </a-form-model-item>
          </a-col>

          <a-col :span="8" >
            <a-form-model-item label="是否自动开标" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="isAutomaticOpen">
              <a-input v-model="model.isAutomaticOpen" placeholder="请输入是否自动开标" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="是否反向竞标" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="isReverseBidding">
              <a-input v-model="model.isReverseBidding" placeholder="请输入是否反向竞标" ></a-input>
            </a-form-model-item>
          </a-col>
        </a-row>
		<a-tabs v-model="activeKey" @change="handleChangeTabs">
		  <a-tab-pane tab="询价单明细" :key="refKeys[0]" :forceRender="true">
			<j-vxe-table
			  keep-source
			  :ref="refKeys[0]"
			  :loading="inquiryRecordTable.loading"
			  :columns="inquiryRecordTable.columns"
			  :dataSource="inquiryRecordTable.dataSource"
			  :maxHeight="300"
			  :disabled="formDisabled"
			  :rowNumber="true"
			  :rowSelection="true"
			  :toolbar="true"
			  :alwaysEdit="true" bordered
			  />
		  </a-tab-pane>
		  <a-tab-pane tab="询价供应商" :key="refKeys[1]" :forceRender="true">
			<j-vxe-table
			  keep-source
			  :ref="refKeys[1]"
			  :loading="inquirySupplierTable.loading"
			  :columns="inquirySupplierTable.columns"
			  :dataSource="inquirySupplierTable.dataSource"
			  :maxHeight="300"
			  :disabled="formDisabled"
			  :rowNumber="true"
			  :rowSelection="true"
			  :toolbar="true"
			  :alwaysEdit="true" bordered
			  />
		  </a-tab-pane>
		</a-tabs>
		<a-card class="card" title="备注和附件" :bordered="false" style="margin-top: 15px;">
		  <a-row>
			<a-col :span="24">
			  <a-form-item label="备注" :labelCol="spans.labelCol1" :wrapperCol="spans.wrapperCol1">
				<a-input v-model="model.comment" placeholder="请输入备注" :rows="3" type='textarea'>
				</a-input>
			  </a-form-item>
			</a-col>
		  </a-row>
		  <a-row>
			<a-col :span="24">
			  <a-form-item label="附件" :labelCol="spans.labelCol1" :wrapperCol="spans.wrapperCol1" required>
				<j-upload v-model="model.attachment" :trigger-change="true"
				  :disabled="readOnly"></j-upload>
			  </a-form-item>
			</a-col>
		  </a-row>
		</a-card>
		</a-form-model>
	</j-form-container>
  </a-spin>
</template>

<script>

  import { getAction } from '@/api/manage'
  import { JVxeTableModelMixin } from '@/mixins/JVxeTableModelMixin.js'
  import { JVXETypes } from '@/components/jeecg/JVxeTable'
  import { getRefPromise,VALIDATE_FAILED} from '@/components/jeecg/JVxeTable/utils/vxeUtils.js'
  import { validateDuplicateValue } from '@/utils/util'
  import JFormContainer from '@/components/jeecg/JFormContainer'
  import { billListMixin } from '../../mixins/billListMixin'
  import { billModalMixin } from '../../mixins/billModalMixin'
  export default {
    name: 'InquiryListForm',
    mixins: [JVxeTableModelMixin,billListMixin, billModalMixin],
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
           inquiryCode: [
              { required: true, message: '请输入询价单号!'},
           ],
        },
        refKeys: ['inquiryRecord', 'inquirySupplier', ],
        tableKeys:['inquiryRecord', 'inquirySupplier', ],
        activeKey: 'inquiryRecord',
        // 询价单明细表
        inquiryRecordTable: {
          loading: false,
          dataSource: [],
          columns: [
            {
              title: '询价记录ID',
              key: 'id',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
              validateRules: [{ required: true, message: '${title}不能为空' }],
            },
            {
              title: '询价单ID',
              key: 'inquiryId',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '询价物品ID',
              key: 'productId',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '记录ID',
              key: 'toRecordId',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '数量',
              key: 'recordNumber',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '单位',
              key: 'recordUnit',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
              validateRules: [{ required: true, message: '${title}不能为空' }],
            },
            {
              title: '交期',
              key: 'recordDeliveryTime',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '交期单位',
              key: 'recordDeliveryUnit',
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
            /*    {
                 title: '删除标志',
                 key: 'delFlag',
                  type: JVXETypes.input,
                 width:"200px",
                 placeholder: '请输入${title}',
                 defaultValue:'',
               },
               {
                 title: '创建用户',
                 key: 'createUser',
                  type: JVXETypes.input,
                 width:"200px",
                 placeholder: '请输入${title}',
                 defaultValue:'',
               },
               {
                 title: '更新用户',
                 key: 'updateUser',
                  type: JVXETypes.input,
                 width:"200px",
                 placeholder: '请输入${title}',
                 defaultValue:'',
               },*/
            {
              title: '备注',
              key: 'comment',
               type: JVXETypes.textarea,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '询价状态(3:已中标)',
              key: 'status',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '中标供应商ID',
              key: 'suppId',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '驳回原因',
              key: 'backReason',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '询价物品名称',
              key: 'productName',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '询价物品规格描述',
              key: 'productSpec',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
          ]
        },
        // 询价供应商表
        inquirySupplierTable: {
          loading: false,
          dataSource: [],
          columns: [
            {
              title: '询比价ID',
              key: 'inquiryId',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '询价记录ID',
              key: 'recordId',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
              validateRules: [{ required: true, message: '${title}不能为空' }],
            },
            {
              title: '是否推荐',
              key: 'isRecommend',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
              validateRules: [{ required: true, message: '${title}不能为空' }],
            },
            {
              title: '状态(0:已受理、1:放弃、2未受理、3过期,4:淘汰)',
              key: 'status',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
              validateRules: [{ required: true, message: '${title}不能为空' }],
            },
            {
              title: '驳回供应商不能再次选择(0:正常、2:重新询价)',
              key: 'delFlag',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
              validateRules: [{ required: true, message: '${title}不能为空' }],
            },
            {
              title: '备注',
              key: 'comment',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
          ]
        },
        url: {
          add: "/srm/inquiryList/add",
          edit: "/srm/inquiryList/edit",
          queryById: "/srm/inquiryList/queryById",
          inquiryRecord: {
            list: '/srm/inquiryList/queryInquiryRecordByMainId'
          },
          inquirySupplier: {
            list: '/srm/inquiryList/queryInquirySupplierByMainId'
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
        this.inquiryRecordTable.dataSource=[]
        this.inquirySupplierTable.dataSource=[]
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
          this.requestSubTableData(this.url.inquiryRecord.list, params, this.inquiryRecordTable)
          this.requestSubTableData(this.url.inquirySupplier.list, params, this.inquirySupplierTable)
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
          inquiryRecordList: allValues.tablesValue[0].tableData,
          inquirySupplierList: allValues.tablesValue[1].tableData,
        }
      },
      validateError(msg){
        this.$message.error(msg)
      },

    }
  }
</script>

<style lang="less" scoped>
  .ant-row.ant-form-item {
    margin-bottom: 12px;
  }
</style>