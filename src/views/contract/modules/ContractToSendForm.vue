<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container >
      <!-- 主表单区域 -->
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-divider orientation="left" style="color: #00A0E9">
            基本信息
          </a-divider>
          <a-col :span="8" >
            <a-form-model-item label="合同标题" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractName">
              <a-input v-model="model.contractName" placeholder="请输入合同标题" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="创建日期" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="createTime">
              <a-input v-model="model.createTime" placeholder="请输入创建日期" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="合同类型" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractType">
              <j-dict-select-tag  v-model="model.contractType" placeholder="请选择合同类型" dictCode="project_type" disabled/>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="合同等级" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractLevel">
              <a-input v-model="model.contractLevel" placeholder="请输入合同等级" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="合同币种" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractCurrency">
              <j-dict-select-tag style="width:100%;" v-model="model.contractCurrency" placeholder="币种"
                                 dictCode="currency_type" :disabled="true"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="合同份数" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractCopiesNumber">
              <a-input-number v-model="model.contractCopiesNumber" placeholder="请输入合同份数" style="width: 100%" disabled/>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="合同金额" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractAmountTax">
              <a-input-number v-model="model.contractAmountTax" placeholder="请输入合同金额" style="width: 100%" disabled/>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="合同税率" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractTaxRate">
              <j-dict-select-tag style="width:100%;" v-model="model.contractTaxRate" placeholder="合同税率"
                                 dictCode="tax_rate" disabled/>
            </a-form-model-item>
          </a-col>

          <a-divider orientation="left" style="color: #00A0E9">
            合同标的
          </a-divider>
          <a-col :span="8">项目编号:<span>{{model.projCode}}</span></a-col>
          <a-col :span="8">项目名称:<span>{{model.projName}}</span></a-col>
          <!--:rowSelection="{selectedRowKeys, onChange: onSelectChange, type: multiple ? 'checkbox':'radio'}"-->
          <a-table
            v-if="model.reqType == '0'"
            ref="table"
            size="middle"
            bordered
            rowKey="id"
            class="j-table-force-nowrap"
            :row-selection="rowSelection"
            :scroll="{x:true}"
            :columns="columns"
            :dataSource="dataSource"
            :pagination="false">
            <template slot='sendQty' slot-scope='text,record'>
              <a-input v-model='record.sendQty' :max='record.qty - record.toSendQty' :disabled='record.qty - record.toSendQty == 0'></a-input>
            </template>
          </a-table>
          <!--:rowSelection="{selectedRowKeys, onChange: onSelectChange, type: multiple ? 'checkbox':'radio'}"-->
          <a-table
            v-if="model.reqType != '0'"
            ref="table"
            size="middle"
            bordered
            rowKey="id"
            class="j-table-force-nowrap"
            :row-selection="rowSelection"
            :scroll="{x:true}"
            :columns="columns1"
            :dataSource="dataSource"
            :pagination="false">
            <template slot='sendQty' slot-scope='text,record'>
              <a-input v-model='record.sendQty' :max='record.qty - record.toSendQty' :disabled='record.qty - record.toSendQty == 0'></a-input>
            </template>
          </a-table>

          <a-divider orientation="left" style="color: #00A0E9">
            合同条款
          </a-divider>
          <a-table
            ref="table"
            size="middle"
            bordered
            rowKey="id"
            class="j-table-force-nowrap"
            :scroll="{x:true}"
            :columns="columns2"
            :dataSource="dataSource2"
            :pagination="false">
            <template slot='termsName' slot-scope='text,record'>
              <a-input v-model='record.termsName' :disabled="formDisabled"></a-input>
            </template>
            <template slot='number' slot-scope='text,record'>
              <a-input v-model='record.number' :disabled="formDisabled"></a-input>
            </template>
            <template slot='termsContent' slot-scope='text,record'>
              <a-input v-model='record.termsContent' :disabled="formDisabled"></a-input>
            </template>
            <template slot='comment' slot-scope='text,record'>
              <a-input v-model='record.comment' type='textarea' :disabled="formDisabled"></a-input>
            </template>
          </a-table>

          <a-divider orientation="left" style="color: #00A0E9">
            支付方式
          </a-divider>
          <a-table
            ref="table"
            size="middle"
            bordered
            rowKey="id"
            class="j-table-force-nowrap"
            :scroll="{x:true}"
            :columns="columns3"
            :dataSource="dataSource3"
            :pagination="false">
            <template slot='termsName' slot-scope='text,record'>
              <a-input v-model='record.termsName' :disabled="formDisabled"></a-input>
            </template>

            <template slot='termsContent' slot-scope='text,record'>
              <a-input v-model='record.termsContent' :disabled="formDisabled"></a-input>
            </template>
            <template slot='payMethod' slot-scope='text,record'>
              <j-dict-select-tag style="width:100%;" v-model="record.payMethod" placeholder="支付方式" dictCode="payMethod":disabled="formDisabled" />
            </template>
            <template slot='payRate' slot-scope='text,record'>
              <a-input-number v-model='record.payRate' :disabled="formDisabled" style='width: 100%'></a-input-number>
            </template>
            <template slot='payType' slot-scope='text,record'>
              <j-dict-select-tag placeholder="请输入收款类型" v-model="record.payType" dictCode="payType" :disabled="formDisabled"/>
            </template>
          </a-table>

          <a-divider orientation="left" style="color: #00A0E9">
            甲方信息
          </a-divider>
          <a-col :span="8" >
            <a-form-model-item label="甲方" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractFirstParty">
              <!--              <a-input v-model="model.contractFirstParty" placeholder="请输入甲方" ></a-input>-->
              <j-dict-select-tag v-model="model.contractFirstParty" placeholder="请输入甲方" dictCode="sys_depart,depart_name,id,parent_id is null or parent_id = ''" disabled/>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方地址" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractFirstAddress">
              <a-input v-model="model.contractFirstAddress" placeholder="请输入甲方地址" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方电话" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractFirstTelphone">
              <a-input v-model="model.contractFirstTelphone" placeholder="请输入甲方电话" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方传真" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractFirstFax">
              <a-input v-model="model.contractFirstFax" placeholder="请输入甲方传真" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方联系人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractFirstContact">
              <a-input v-model="model.contractFirstContact" placeholder="请输入甲方联系人" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方法人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractFirstLegalPerson">
              <a-input v-model="model.contractFirstLegalPerson" placeholder="请输入甲方法人" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方代理人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractFirstAgent">
              <a-input v-model="model.contractFirstAgent" placeholder="请输入甲方代理人" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方开户行" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractFirstOpeningBank">
              <a-input v-model="model.contractFirstOpeningBank" placeholder="请输入甲方开户行" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方银行账号" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractFirstBankAccount">
              <a-input v-model="model.contractFirstBankAccount" placeholder="请输入甲方银行账号" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方邮政编码" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractFirstPostCode">
              <a-input v-model="model.contractFirstPostCode" placeholder="请输入甲方邮政编码" disabled></a-input>
            </a-form-model-item>
          </a-col>

          <a-divider orientation="left" style="color: #00A0E9">
            乙方信息
          </a-divider>
          <a-col :span="8" >
            <a-form-model-item label="乙方" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSecondParty">
              <a-input v-model="model.contractSecondParty" placeholder="请输入乙方" :disabled="formDisabled"></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="乙方地址" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSecondAddress">
              <a-input v-model="model.contractSecondAddress" placeholder="请输入乙方地址" :disabled="formDisabled"></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="乙方电话" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSecondTelphone">
              <a-input v-model="model.contractSecondTelphone" placeholder="请输入乙方电话" :disabled="formDisabled"></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="乙方传真" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSecondFax">
              <a-input v-model="model.contractSecondFax" placeholder="请输入乙方传真" :disabled="formDisabled"></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="乙方联系人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSecondContact">
              <a-input v-model="model.contractSecondContact" placeholder="请输入乙方联系人" :disabled="formDisabled"></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="乙方法人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSecondLegalPerson">
              <a-input v-model="model.contractSecondLegalPerson" placeholder="请输入乙方法人" :disabled="formDisabled"></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="乙方代理人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSecondAgent">
              <a-input v-model="model.contractSecondAgent" placeholder="请输入乙方代理人" :disabled="formDisabled"></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="乙方开户行" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSecondOpeningBank">
              <a-input v-model="model.contractSecondOpeningBank" placeholder="请输入乙方开户行" :disabled="formDisabled"></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="乙方银行账号" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSecondBankAccount">
              <a-input v-model="model.contractSecondBankAccount" placeholder="请输入乙方银行账号" :disabled="formDisabled"></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="乙方邮政编码" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractSecondPostCode">
              <a-input v-model="model.contractSecondPostCode" placeholder="请输入乙方邮政编码" :disabled="formDisabled"></a-input>
            </a-form-model-item>
          </a-col>
          <a-divider orientation="left" style="color: #00A0E9">
            附件
          </a-divider>
          <a-col :span="24">
            <a-form-item label="合同word" :labelCol="spans.labelCol1" :wrapperCol="spans.wrapperCol1">
              <j-upload v-model="model.wordAttachment" :trigger-change="true" disabled></j-upload>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="其他附件资料" :labelCol="spans.labelCol1" :wrapperCol="spans.wrapperCol1">
              <j-upload v-model="model.otherAttachment" :trigger-change="true" disabled></j-upload>
            </a-form-item>
          </a-col>
          <a-col :span="12" >
            <a-form-model-item label="备注" :labelCol="spans.labelCol2" :wrapperCol="spans.wrapperCol2" prop="comment">
              <a-input v-model="model.comment" placeholder="请输入备注" type="textarea" disabled></a-input>
            </a-form-model-item>
          </a-col>

        </a-row>
      </a-form-model>
    </j-form-container>

  </a-spin>
</template>

<script>

import { getAction,httpAction  } from '@/api/manage'
import JFormContainer from '@/components/jeecg/JFormContainer'
import { billModalMixin } from '../../mixins/billModalMixin'
import { isNullOrEmpty } from '@/utils/util'

export default {
  name: 'ContractToSendForm',
  mixins: [billModalMixin],
  components: {
    JFormContainer,
  },
  data() {
    return {
      multiple:true,
      selectedRowKeys: [],
      selectionRows: [],
      dataSource3:[],
      columns3:[
        {
          title: '条款名称',
          dataIndex: 'termsName',
          width:140,
          scopedSlots: {
            customRender: 'termsName'
          },
        },
        {
          title: '条款内容',
          dataIndex: 'termsContent',
          width:180,
          scopedSlots: {
            customRender: 'termsContent'
          },
        },
        {
          title: '支付方式',
          dataIndex: 'payMethod',
          width:120,
          scopedSlots: {
            customRender: 'payMethod'
          },
        },
        {
          title: '支付比例',
          dataIndex: 'payRate',
          width:120,
          scopedSlots: {
            customRender: 'payRate'
          },
        },
        {
          title: '收款类型',
          dataIndex: 'payType',
          width:120,
          scopedSlots: {
            customRender: 'payType'
          },
        },
      ],
      dataSource2:[],
      columns2:[
        {
          title: '条款名称',
          dataIndex: 'termsName',
          width:140,
          scopedSlots: {
            customRender: 'termsName'
          },
        },
        {
          title: '条款序号',
          dataIndex: 'number',
          width:120,
          scopedSlots: {
            customRender: 'number'
          },
        },
        {
          title: '合同条款',
          dataIndex: 'termsContent',
          width:180,
          scopedSlots: {
            customRender: 'termsContent'
          },
        },
        {
          title: '备注',
          dataIndex: 'comment',
          width:180,
          scopedSlots: {
            customRender: 'comment'
          },
        },
      ],
      columns1:[
        {
          title: '序号',
          dataIndex: '',
          key:'rowIndex',
          width:60,
          align:"center",
          customRender:function (t,r,index) {
            return parseInt(index)+1;
          }
        },
        {
          title: '购置名称',
          dataIndex: 'prodName',
          width:140,
        },
        {
          title: '数量',
          dataIndex: 'qty',
          width:120,
        },
        {
          title: '已发货数量',
          dataIndex: 'toSendQty',
          width:120,
        },
        {
          title: '发货数量',
          dataIndex: 'sendQty',
          width:120,
          scopedSlots: {
            customRender: 'sendQty'
          },
        },
        {
          title: '单价',
          dataIndex: 'priceTax',
          width:120,
        },
        {
          title: '小计',
          dataIndex: 'amountTax',
          width:120,
        },
        {
          title: '交货日期',
          dataIndex: 'leadTime',
          width:120,
        },
      ],
      columns:[
        {
          title: '序号',
          dataIndex: '',
          key:'rowIndex',
          width:60,
          align:"center",
          customRender:function (t,r,index) {
            return parseInt(index)+1;
          }
        },
        {
          title: '设备编号',
          dataIndex: 'prodCode',
          width:120,
        },
        {
          title: '设备名称',
          dataIndex: 'prodName',
          width:120,
        },
        {
          title: '规格、型号参数',
          dataIndex: 'speType',
          width:200,
        },
        // {
        //   title: '品牌',
        //   dataIndex: 'brandName',
        //   width:120,
        // },
        {
          title: '设备产能',
          dataIndex: 'capacity',
          width:120,
        },
        {
          title: '数量',
          dataIndex: 'qty',
          width:120,
          customRender: function(t, r, index) {
            if(r.unitId_dictText != null && r.unitId_dictText != '' && r.unitId_dictText != undefined){
              return r.qty + r.unitId_dictText;
            }else{
              return r.qty;
            }
          },
        },
        {
          title: '已发货数量',
          dataIndex: 'toSendQty',
          width:120,
        },
        {
          title: '发货数量',
          dataIndex: 'sendQty',
          width:120,
          scopedSlots: {
            customRender: 'sendQty'
          },
        },
        {
          title: '设备单价',
          dataIndex: 'priceTax',
          width:120,
        },
        {
          title: '小计',
          dataIndex: 'amountTax',
          width:120,
        },
        {
          title: '交货日期',
          dataIndex: 'leadTime',
          width:120,
        },
      ],
      dataSource:[],
      confirmLoading:false,
      model:{
      },
      // 新增时子表默认添加几行空数据
      addDefaultRowNum: 1,
      validatorRules: {
        contractName: [
          { required: true, message: '请输入合同标题!'},
        ],
        contractType: [
          { required: true, message: '请选择合同类型!'},
        ],
        contractCurrency: [
          { required: true, message: '请选择合同币种!'},
        ],
        contractCopiesNumber: [
          { required: true, message: '请输入合同分数!'},
        ],
        contractTaxRate: [
          { required: true, message: '请选择合同税率!'},
        ],
        contractFirstParty:[
          { required: true, message: '请输入甲方!'},
        ],
        contractFirstAddress:[
          { required: true, message: '请输入甲方地址!'},
        ],
        contractFirstTelphone:[
          { required: true, message: '请输入甲方电话!'},
        ],
        contractFirstFax:[
          { required: true, message: '请输入甲方传真!'},
        ],
        contractFirstContact:[
          { required: true, message: '请输入甲方联系人!'},
        ],
        contractFirstLegalPerson:[
          { required: true, message: '请输入甲方法人!'},
        ],
      },
      refKeys: [],
      url: {
        add: "/srm/contractBase/add",
        draft: "/srm/contractBase/draft",
        edit: "/srm/contractBase/toSend",
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
    rowSelection() {
      return {
        onChange: (selectedRowKeys, selectedRows) => {
          this.selectionRows = selectedRows;
          this.selectedRowKeys = selectedRowKeys;
          console.log(`selectedRowKeys: ${selectedRowKeys}`, 'selectedRows: ', selectedRows);
        },
        getCheckboxProps: record => ({
          props: {
            disabled: record.qty === record.toSendQty, // Column configuration not to be checked
          },
        }),
      };
    },
  },
  created () {
  },
  methods: {
    onSelectChange(selectedRowKeys, selectionRows) {
      this.selectedRowKeys = selectedRowKeys;
      this.selectionRows = selectionRows;
    },
    handleOk(){
      const that = this
      // 触发表单验证
      that.$refs.form.validate(valid => {
        if (valid) {
          let post = "";
          let url = "";
          post = 'put';
          url = this.url.edit;
          let selectRows = that.selectionRows;
          if(selectRows == null || selectRows.length == 0){
            that.$message.error("请选择需要发货的明细");
            return
          }
          for(let i = 0; i < selectRows.length; i++){
            if(isNullOrEmpty(selectRows[i].sendQty)){
              that.$message.error("第" + (i+1) + "行,请输入发货数量");
              return
            }
          }
          that.model.contractObjectList = selectRows;
          that.$confirm({
            content: `是否确认提交`,
            onOk: () => {
              httpAction(url,that.model,post).then((res)=> {
                if (res.success) {
                  that.$message.success(res.message);
                  that.$emit('ok');
                } else {
                  that.$message.warning(res.message);
                }
              })
            }
          })
        }else{
          return false;
        }
      })
    },

    editTwo(record) {
      this.dataSource = [];
      this.dataSource2 = [];
      this.dataSource3 = [];
      this.selectedRowKeys = [];
      this.selectionRows = [];
      this.model = Object.assign({}, record);
      this.model.approver = this.$store.getters.userInfo.realname;
      this.model.approveComment = "";
      this.getContractDetailList(this.model.id);
      this.getProject(this.model.projectId);
      this.getPurchaseRequest(this.model.id);
      this.getPayList(this.model.id);
      this.getTerms(this.model.id);
      this.getApprove(this.model.id);
    },
    getApprove(contractId){
      let url = "/srm/contractBase/queryApprove";
      getAction(url,{id:contractId}).then(res => {
        if(res.result != null){
          this.model.approveComment = res.result.approveComment;
        }
      })
    },
    getTerms(contractId){
      let url = "/srm/contractBase/queryContractTermsByMainId";
      getAction(url,{id:contractId}).then(res => {
        if(res.result != null && res.result.length > 0){
          this.dataSource2 = res.result;
        }
      })
    },

    getPayList(contractId){
      let url = "/srm/contractBase/queryContractPayStepByMainId";
      getAction(url,{id:contractId}).then(res => {
        if(res.result != null && res.result.length > 0){
          this.dataSource3 = res.result;
        }
      })
    },
    getPurchaseRequest(id){
      let url = "/srm/purchaseRequestMain/getPurchaseRequest";
      getAction(url,{contractId:id}).then(res => {
        this.model.reqType = res.result.reqType;
      })
    },
    getProject(projectId){
      let url = "/srm/projBase/queryById";
      getAction(url,{id:projectId}).then(res => {
        this.model.projCode = res.result.projCode;
        this.model.projName = res.result.projName;
      })
    },
    getContractDetailList(id){
      let url = "/srm/contractBase/getContractDetailList";
      getAction(url,{id:id}).then(res => {
        if(res.result != null && res.result.length > 0){
          this.dataSource = res.result;
        }
      })
    },
    formatDate(){
      let getDate = new Date();
      let year = getDate.getFullYear();
      let month = getDate.getMonth() + 1;
      let day = getDate.getDate();
      if(month < 10){
        month =  "0" + month;
      }
      return year + "-" + month + "-" + day;
    }
  }
}
</script>

<style scoped>
</style>