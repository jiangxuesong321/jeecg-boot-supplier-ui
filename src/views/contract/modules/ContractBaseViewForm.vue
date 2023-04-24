<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <!-- 主表单区域 -->
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="8" >
            <a-form-model-item label="合同编号" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractNumber">
              <a-input v-model="model.contractNumber" placeholder="请输入合同编号" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="合同名称" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractName">
              <a-input v-model="model.contractName" placeholder="请输入合同名称" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="合同类型" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractType">
              <a-input v-model="model.contractType" placeholder="请输入合同类型" ></a-input>
            </a-form-model-item>
          </a-col>
<!--          <a-col :span="8" >
            <a-form-model-item label="项目ID" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="projectId">
              <a-input v-model="model.projectId" placeholder="请输入项目ID" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="需求ID" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="requestId">
              <a-input v-model="model.requestId" placeholder="请输入需求ID" ></a-input>
            </a-form-model-item>
          </a-col> -->
          <a-col :span="8" >
            <a-form-model-item label="合同币种" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractCurrency">
              <a-input v-model="model.contractCurrency" placeholder="请输入合同币种" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="合同等级" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractLevel">
              <a-input v-model="model.contractLevel" placeholder="请输入合同等级" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="合同状态" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractStatus">
              <a-input v-model="model.contractStatus" placeholder="请输入合同状态" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="合同份数" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractCopiesNumber">
              <a-input-number v-model="model.contractCopiesNumber" placeholder="请输入合同份数" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="合同签订日期" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSignDate">
              <j-date placeholder="请选择合同签订日期" v-model="model.contractSignDate" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="签订地点" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSignAddress">
              <a-input v-model="model.contractSignAddress" placeholder="请输入签订地点" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="合同生效日期" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractValidDate">
              <j-date placeholder="请选择合同生效日期" v-model="model.contractValidDate" style="width: 100%" />
            </a-form-model-item>
          </a-col>
<!--          <a-col :span="8" >
            <a-form-model-item label="合同金额原币" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractAmount">
              <a-input-number v-model="model.contractAmount" placeholder="请输入合同金额原币" style="width: 100%" />
            </a-form-model-item>
          </a-col> -->
          <a-col :span="8" >
            <a-form-model-item label="合同金额" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractAmountTax">
              <a-input-number v-model="model.contractAmountTax" placeholder="请输入合同金额" style="width: 100%" />
            </a-form-model-item>
          </a-col>
<!--          <a-col :span="8" >
            <a-form-model-item label="合同金额本币" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractAmountLocal">
              <a-input-number v-model="model.contractAmountLocal" placeholder="请输入合同金额本币" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="合同含税金额本币" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractAmountTaxLocal">
              <a-input-number v-model="model.contractAmountTaxLocal" placeholder="请输入合同含税金额本币" style="width: 100%" />
            </a-form-model-item>
          </a-col> -->
<!--          <a-col :span="8" >
            <a-form-model-item label="合同汇率" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractExchangeRate">
              <a-input-number v-model="model.contractExchangeRate" placeholder="请输入合同汇率" style="width: 100%" />
            </a-form-model-item>
          </a-col> -->
          <a-col :span="8" >
            <a-form-model-item label="合同税率" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractTaxRate">
              <a-input-number v-model="model.contractTaxRate" placeholder="请输入合同税率" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractFirstParty">
              <a-input v-model="model.contractFirstParty" placeholder="请输入甲方" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="乙方" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSecondParty">
              <a-input v-model="model.contractSecondParty" placeholder="请输入乙方" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方地址" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractFirstAddress">
              <a-input v-model="model.contractFirstAddress" placeholder="请输入甲方地址" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方电话" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractFirstTelphone">
              <a-input v-model="model.contractFirstTelphone" placeholder="请输入甲方电话" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方传真" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractFirstFax">
              <a-input v-model="model.contractFirstFax" placeholder="请输入甲方传真" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方联系人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractFirstContact">
              <a-input v-model="model.contractFirstContact" placeholder="请输入甲方联系人" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方开户行" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractFirstOpeningBank">
              <a-input v-model="model.contractFirstOpeningBank" placeholder="请输入甲方开户行" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方银行账号" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractFirstBankAccount">
              <a-input v-model="model.contractFirstBankAccount" placeholder="请输入甲方银行账号" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方法人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractFirstLegalPerson">
              <a-input v-model="model.contractFirstLegalPerson" placeholder="请输入甲方法人" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方代理人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractFirstAgent">
              <a-input v-model="model.contractFirstAgent" placeholder="请输入甲方代理人" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方邮政编码" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractFirstPostCode">
              <a-input v-model="model.contractFirstPostCode" placeholder="请输入甲方邮政编码" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="乙方地址" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSecondAddress">
              <a-input v-model="model.contractSecondAddress" placeholder="请输入乙方地址" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="乙方电话" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSecondTelphone">
              <a-input v-model="model.contractSecondTelphone" placeholder="请输入乙方电话" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="乙方传真" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSecondFax">
              <a-input v-model="model.contractSecondFax" placeholder="请输入乙方传真" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="乙方联系人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSecondContact">
              <a-input v-model="model.contractSecondContact" placeholder="请输入乙方联系人" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="乙方开户行" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSecondOpeningBank">
              <a-input v-model="model.contractSecondOpeningBank" placeholder="请输入乙方开户行" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="乙方银行账号" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSecondBankAccount">
              <a-input v-model="model.contractSecondBankAccount" placeholder="请输入乙方银行账号" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="乙方法人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSecondLegalPerson">
              <a-input v-model="model.contractSecondLegalPerson" placeholder="请输入乙方法人" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="乙方代理人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSecondAgent">
              <a-input v-model="model.contractSecondAgent" placeholder="请输入乙方代理人" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="乙方邮政编码" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSecondPostCode">
              <a-input v-model="model.contractSecondPostCode" placeholder="请输入乙方邮政编码" ></a-input>
            </a-form-model-item>
          </a-col>
<!--          <a-col :span="8" >
            <a-form-model-item label="备注" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="comment">
              <a-input v-model="model.comment" placeholder="请输入备注" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="排序" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="sort">
              <a-input-number v-model="model.sort" placeholder="请输入排序" style="width: 100%" />
            </a-form-model-item>
          </a-col> -->
<!--          <a-col :span="8" >
            <a-form-model-item label="租户ID" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="tenantId">
              <a-input v-model="model.tenantId" placeholder="请输入租户ID" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="删除标志位" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="delFlag">
              <a-input v-model="model.delFlag" placeholder="请输入删除标志位" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="创建人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="createUser">
              <a-input v-model="model.createUser" placeholder="请输入创建人" ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="更新人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="updateUser">
              <a-input v-model="model.updateUser" placeholder="请输入更新人" ></a-input>
            </a-form-model-item>
          </a-col> -->
        </a-row>
      </a-form-model>
    </j-form-container>
      <!-- 子表单区域 -->
    <a-tabs v-model="activeKey" @change="handleChangeTabs">
      <a-tab-pane tab="合同标的" :key="refKeys[0]" :forceRender="true">
        <j-vxe-table
          keep-source
          :ref="refKeys[0]"
          :loading="contractObjectTable.loading"
          :columns="contractObjectTable.columns"
          :dataSource="contractObjectTable.dataSource"
          :maxHeight="300"
          :disabled="formDisabled"
          :rowNumber="true"
          :rowSelection="true"
          :toolbar="true"
          />
      </a-tab-pane>
      <a-tab-pane tab="合同收款阶段" :key="refKeys[1]" :forceRender="true">
        <j-vxe-table
          keep-source
          :ref="refKeys[1]"
          :loading="contractPayStepTable.loading"
          :columns="contractPayStepTable.columns"
          :dataSource="contractPayStepTable.dataSource"
          :maxHeight="300"
          :disabled="formDisabled"
          :rowNumber="true"
          :rowSelection="true"
          :toolbar="true"
          />
      </a-tab-pane>
      <a-tab-pane tab="合同条款" :key="refKeys[2]" :forceRender="true">
        <j-vxe-table
          keep-source
          :ref="refKeys[2]"
          :loading="contractTermsTable.loading"
          :columns="contractTermsTable.columns"
          :dataSource="contractTermsTable.dataSource"
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
  import { billListMixin } from '../../mixins/billListMixin'
  import { billModalMixin } from '../../mixins/billModalMixin'

  export default {
    name: 'ContractBaseForm',
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
           delFlag: [
              { required: true, message: '请输入删除标志位!'},
           ],
           createUser: [
              { required: true, message: '请输入创建人!'},
           ],
           updateUser: [
              { required: true, message: '请输入更新人!'},
           ],
        },
        refKeys: ['contractObject', 'contractPayStep', 'contractTerms', ],
        tableKeys:['contractObject', 'contractPayStep', 'contractTerms', ],
        activeKey: 'contractObject',
        // 合同标的
        contractObjectTable: {
          loading: false,
          dataSource: [],
          columns: [
            {
              title: '合同ID',
              key: 'contractId',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '标的类型',
              key: 'objectType',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '设备编号',
              key: 'prodCode',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '设备名称',
              key: 'prodName',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '合同未税单价原币',
              key: 'contractPrice',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '合同含税单价原币',
              key: 'contractPriceTax',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '合同总额未税原币',
              key: 'contractAmount',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '合同总额含税原币',
              key: 'contractAmountTax',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '税率',
              key: 'contractTaxRate',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '设备品牌',
              key: 'prodBrand',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '设备规格型号',
              key: 'prodSpecType',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '汇率',
              key: 'exchangeRate',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '合同未税单价本币',
              key: 'contractPriceLocal',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '合同含税单价本币',
              key: 'contractPriceTaxLocal',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '合同总额未税本币',
              key: 'contractAmountLocal',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '合同总额含税本币',
              key: 'contractAmountTaxLocal',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '需求日期',
              key: 'requireDate',
              type: JVXETypes.date,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '计划交期',
              key: 'planDeliveryDate',
              type: JVXETypes.date,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
          ]
        },
        // 合同收款阶段
        contractPayStepTable: {
          loading: false,
          dataSource: [],
          columns: [
            {
              title: '合同ID',
              key: 'contractId',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '收款类型',
              key: 'payType',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '支付方式',
              key: 'payMethod',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '收款比例(%)',
              key: 'payRate',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '备注',
              key: 'comment',
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
            {
              title: '租户ID',
              key: 'tenantId',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '删除标志位',
              key: 'delFlag',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
              validateRules: [{ required: true, message: '${title}不能为空' }],
            },
            {
              title: '创建人',
              key: 'createUser',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
              validateRules: [{ required: true, message: '${title}不能为空' }],
            },
            {
              title: '更新人',
              key: 'updateUser',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
              validateRules: [{ required: true, message: '${title}不能为空' }],
            },
          ]
        },
        // 合同条款
        contractTermsTable: {
          loading: false,
          dataSource: [],
          columns: [
            {
              title: '合同ID',
              key: 'contractId',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '合同条款序号',
              key: 'contractTermsNo',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '合同条款类型',
              key: 'contractTermsType',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '合同条款描述',
              key: 'contractTermsTypeDesc',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '合同条款内容',
              key: 'contractTermsContent',
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
            {
              title: '租户ID',
              key: 'tenantId',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
            },
            {
              title: '删除标志位',
              key: 'delFlag',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
              validateRules: [{ required: true, message: '${title}不能为空' }],
            },
            {
              title: '创建人',
              key: 'createUser',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
              validateRules: [{ required: true, message: '${title}不能为空' }],
            },
            {
              title: '更新人',
              key: 'updateUser',
               type: JVXETypes.input,
              width:"200px",
              placeholder: '请输入${title}',
              defaultValue:'',
              validateRules: [{ required: true, message: '${title}不能为空' }],
            },
          ]
        },
        url: {
          add: "/srm/contractBase/add",
          edit: "/srm/contractBase/edit",
          queryById: "/srm/contractBase/queryById",
          contractObject: {
            list: '/srm/contractBase/queryContractObjectByMainId'
          },
          contractPayStep: {
            list: '/srm/contractBase/queryContractPayStepByMainId'
          },
          contractTerms: {
            list: '/srm/contractBase/queryContractTermsByMainId'
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
        this.contractObjectTable.dataSource=[]
        this.contractPayStepTable.dataSource=[]
        this.contractTermsTable.dataSource=[]
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
          this.requestSubTableData(this.url.contractObject.list, params, this.contractObjectTable)
          this.requestSubTableData(this.url.contractPayStep.list, params, this.contractPayStepTable)
          this.requestSubTableData(this.url.contractTerms.list, params, this.contractTermsTable)
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
          contractObjectList: allValues.tablesValue[0].tableData,
          contractPayStepList: allValues.tablesValue[1].tableData,
          contractTermsList: allValues.tablesValue[2].tableData,
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
<style lang="less" scoped>
  .ant-row.ant-form-item {
    margin-bottom: 12px;
  }
</style>