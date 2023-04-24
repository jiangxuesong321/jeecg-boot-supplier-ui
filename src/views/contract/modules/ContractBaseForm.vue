<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container >
      <!-- 主表单区域 -->
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-tag color="red"
               style="line-height: 25px;padding: 5px 10px;font-size: 14px;float: left;margin-top: -10px;margin-bottom: 10px"
               v-if="model.contractStatus == '1' && model.zhApproveComment != null && model.zhApproveComment != undefined && model.zhApproveComment != ''">
          甲方驳回原因:{{model.zhApproveComment}}
        </a-tag>
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
              <a-input-number v-model="model.contractAmountTax" placeholder="请输入合同金额" style="width: 100%" disabled
                              :formatter="value => `${value}`.replace(/\B(?=(\d{3})+(?!\d))/g, ',')"
                              :parser="value => value.replace(/\$\s?|(,*)/g, '')"/>
            </a-form-model-item>
          </a-col>
<!--          <a-col :span="8">-->
<!--            <a-form-model-item label="合同税率" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractTaxRate">-->
<!--              <j-dict-select-tag style="width:100%;" v-model="model.contractTaxRate" placeholder="合同税率"-->
<!--                                 dictCode="tax_rate" disabled/>-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->

          <a-divider orientation="left" style="color: #00A0E9">
            合同标的
          </a-divider>
          <a-col :span="8">项目编号:<span>{{model.projCode}}</span></a-col>
          <a-col :span="8">项目名称:<span>{{model.projName}}</span></a-col>

          <a-table
            v-if="model.reqType == '0'"
            ref="table"
            size="middle"
            bordered
            rowKey="id"
            class="j-table-force-nowrap"
            :scroll="{x:true}"
            :columns="columns"
            :dataSource="dataSource"
            :pagination="false">
            <template slot='action' slot-scope='text,record'>
              <a @click='handleSplit(record)' :disabled="formDisabled">数量拆分</a>
            </template>
          </a-table>
          <a-table
            v-if="model.reqType != '0'"
            ref="table"
            size="middle"
            bordered
            rowKey="id"
            class="j-table-force-nowrap"
            :scroll="{x:true}"
            :columns="columns1"
            :dataSource="dataSource"
            :pagination="false">
            <template slot='action' slot-scope='text,record'>
              <a @click='handleSplit(record)' :disabled="formDisabled">数量拆分</a>
            </template>
          </a-table>

          <a-divider orientation="left" style="color: #00A0E9">
            合同条款
          </a-divider>
          <a-button @click="addcolumns2" type="primary" icon="plus" style='float: right;z-index: 999' :disabled="formDisabled">新增</a-button>
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
            <template slot='action' slot-scope='text,record,index'>
              <a @click='delRow2(record,index)' :disabled="formDisabled">删除</a>
            </template>
          </a-table>

          <a-divider orientation="left" style="color: #00A0E9">
            支付方式
          </a-divider>
          <a-button @click="addcolumns3" type="primary" icon="plus" style='float: right;z-index: 999' :disabled="formDisabled">新增</a-button>
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
            <template slot='action' slot-scope='text,record,index'>
              <a @click='delRow3(record,index)' :disabled="formDisabled">删除</a>
            </template>
          </a-table>

          <a-divider orientation="left" style="color: #00A0E9">
            甲方信息
          </a-divider>
          <a-col :span="8" >
            <a-form-model-item label="甲方" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              <!--              <a-input v-model="model.contractFirstParty" placeholder="请输入甲方" ></a-input>-->
              <j-dict-select-tag v-model="model.contractFirstParty" placeholder="请输入甲方" dictCode="sys_depart,depart_name,id,parent_id is null or parent_id = ''" disabled/>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方地址" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              <a-input v-model="model.contractFirstAddress" placeholder="请输入甲方地址" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方电话" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              <a-input v-model="model.contractFirstTelphone" placeholder="请输入甲方电话" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方传真" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              <a-input v-model="model.contractFirstFax" placeholder="请输入甲方传真" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方联系人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              <a-input v-model="model.contractFirstContact" placeholder="请输入甲方联系人" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方法人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              <a-input v-model="model.contractFirstLegalPerson" placeholder="请输入甲方法人" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方代理人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              <a-input v-model="model.contractFirstAgent" placeholder="请输入甲方代理人" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方开户行" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              <a-input v-model="model.contractFirstOpeningBank" placeholder="请输入甲方开户行" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方银行账号" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              <a-input v-model="model.contractFirstBankAccount" placeholder="请输入甲方银行账号" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="甲方邮政编码" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
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
            <a-form-item label="合同附件" :labelCol="spans.labelCol1" :wrapperCol="spans.wrapperCol1">
              <j-upload v-model="model.contractAttachment" :trigger-change="true" ></j-upload>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="规格书附件" :labelCol="spans.labelCol1" :wrapperCol="spans.wrapperCol1">
              <j-upload v-model="model.specificationAttachment" :trigger-change="true" ></j-upload>
            </a-form-item>
          </a-col>
<!--          <a-col :span="24">
            <a-form-item label="其他附件资料" :labelCol="spans.labelCol1" :wrapperCol="spans.wrapperCol1">
              <j-upload v-model="model.otherAttachment" :trigger-change="true" disabled></j-upload>
            </a-form-item>
          </a-col>-->
          <a-col :span="12" >
            <a-form-model-item label="备注" :labelCol="spans.labelCol2" :wrapperCol="spans.wrapperCol2" prop="comment">
              <a-input v-model="model.comment" placeholder="请输入备注" type="textarea" disabled></a-input>
            </a-form-model-item>
          </a-col>

<!--          <a-divider orientation="left" style="color: #00A0E9">-->
<!--            合同审核-->
<!--          </a-divider>-->
<!--          <a-col :span="24">-->
<!--            <a-form-model-item label="审核备注" :labelCol="spans.labelCol1" :wrapperCol="spans.wrapperCol1">-->
<!--              <a-input v-model="model.approveComment" placeholder="请输入备注" type="textarea" style="width: 45%" :disabled="formDisabled"></a-input>-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
        </a-row>
      </a-form-model>

      <contract-object-qty-modal ref='modalForm' @ok='back'></contract-object-qty-modal>
    </j-form-container>

  </a-spin>
</template>

<script>

import { getAction,httpAction  } from '@/api/manage'
import JFormContainer from '@/components/jeecg/JFormContainer'
import { billModalMixin } from '../../mixins/billModalMixin'
import { isNullOrEmpty } from '@/utils/util'
import ContractObjectQtyModal from '@views/contract/modules/ContractObjectQtyModal'
import {
  iegAmount
} from '@/utils/util'
export default {
  name: 'ContractBaseForm',
  mixins: [billModalMixin],
  components: {
    ContractObjectQtyModal,
    JFormContainer,
  },
  data() {
    return {
      model:{
      },
      dataSource3:[],
      columns3:[
        {
          title: '收款类型',
          dataIndex: 'payType',
          width:120,
          scopedSlots: {
            customRender: 'payType'
          },
        },
        // {
        //   title: '条款名称',
        //   dataIndex: 'termsName',
        //   width:140,
        //   scopedSlots: {
        //     customRender: 'termsName'
        //   },
        // },
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
          title: '支付比例(%)',
          dataIndex: 'payRate',
          width:120,
          scopedSlots: {
            customRender: 'payRate'
          },
        },

        {
          title: '操作',
          dataIndex: 'action',
          width:100,
          scopedSlots: {
            customRender: 'action'
          },
        },
      ],
      dataSource2:[],
      columns2:[
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
          title: '条款名称',
          dataIndex: 'termsName',
          width:140,
          scopedSlots: {
            customRender: 'termsName'
          },
        },
        // {
        //   title: '条款序号',
        //   dataIndex: 'number',
        //   width:120,
        //   scopedSlots: {
        //     customRender: 'number'
        //   },
        // },
        {
          title: '条款内容',
          dataIndex: 'termsContent',
          width:180,
          scopedSlots: {
            customRender: 'termsContent'
          },
        },
        // {
        //   title: '备注',
        //   dataIndex: 'comment',
        //   width:180,
        //   scopedSlots: {
        //     customRender: 'comment'
        //   },
        // },
        {
          title: '操作',
          dataIndex: 'action',
          width:100,
          scopedSlots: {
            customRender: 'action'
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
          title: '税率(%)',
          dataIndex: 'contractTaxRate',
          width:120,
          customRender: (t,r,index) => {
            if(t == '100'){
              return '其他';
            }else{
              return t;
            }
          }
        },
        {
          title: '单价',
          dataIndex: 'priceTax',
          width:120,
          customRender: (t,r,index) => {
            let _this = this;
            let icon = "";
            if(_this.model.contractCurrency == 'RMB'){
              icon = '¥';
            }else if(_this.model.contractCurrency == 'JPY'){
              icon = 'Ұ';
            }else if(_this.model.contractCurrency == 'USD'){
              icon = '$';
            }else if(_this.model.contractCurrency == 'EUR'){
              icon = '€';
            }
            const obj = {
              children: icon + iegAmount(t,'total'),
              attrs: {},
            };
            return obj;
            // return icon + iegAmount(t,'total')
          },
        },
        {
          title: '小计',
          dataIndex: 'amountTax',
          width:120,
          customRender: (t,r,index) => {
            let _this = this;
            let icon = "";
            if(_this.model.contractCurrency == 'RMB'){
              icon = '¥';
            }else if(_this.model.contractCurrency == 'JPY'){
              icon = 'Ұ';
            }else if(_this.model.contractCurrency == 'USD'){
              icon = '$';
            }else if(_this.model.contractCurrency == 'EUR'){
              icon = '€';
            }
            const obj = {
              children: icon + iegAmount(t,'total'),
              attrs: {},
            };
            return obj;
            // return icon + iegAmount(t,'total')
          },
        },
        {
          title: '操作',
          dataIndex: 'action',
          width:120,
          scopedSlots: {
            customRender: 'action'
          },
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
        // {
        //   title: '设备编号',
        //   dataIndex: 'prodCode',
        //   width:120,
        // },
        {
          title: '设备名称',
          dataIndex: 'prodName',
          width:120,
        },
        {
          title: '规格、型号参数',
          dataIndex: 'speType',
          width:200,
          ellipsis:true
        },
        {
          title: '品牌',
          dataIndex: 'prodBrand',
          width:200,
          ellipsis:true
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
          title: '税率(%)',
          dataIndex: 'contractTaxRate',
          width:120,
          customRender: (t,r,index) => {
            if(t == '100'){
              return '其他';
            }else{
              return t;
            }
          }
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
          title: '设备单价',
          dataIndex: 'priceTax',
          width:120,
          customRender: (t,r,index) => {
            let _this = this;
            let icon = "";
            if(_this.model.contractCurrency == 'RMB'){
              icon = '¥';
            }else if(_this.model.contractCurrency == 'JPY'){
              icon = 'Ұ';
            }else if(_this.model.contractCurrency == 'USD'){
              icon = '$';
            }else if(_this.model.contractCurrency == 'EUR'){
              icon = '€';
            }
            const obj = {
              children: icon + iegAmount(t,'total'),
              attrs: {},
            };
            return obj;
            // return icon + iegAmount(t,'total')
          },
        },
        {
          title: '小计',
          dataIndex: 'amountTax',
          width:120,
          customRender: (t,r,index) => {
            let _this = this;
            let icon = "";
            if(_this.model.contractCurrency == 'RMB'){
              icon = '¥';
            }else if(_this.model.contractCurrency == 'JPY'){
              icon = 'Ұ';
            }else if(_this.model.contractCurrency == 'USD'){
              icon = '$';
            }else if(_this.model.contractCurrency == 'EUR'){
              icon = '€';
            }
            const obj = {
              children: icon + iegAmount(t,'total'),
              attrs: {},
            };
            return obj;
            // return icon + iegAmount(t,'total')
          },
        },
        // {
        //   title: '交货日期',
        //   dataIndex: 'leadTime',
        //   width:120,
        // },
        {
          title: '操作',
          dataIndex: 'action',
          width:120,
          scopedSlots: {
            customRender: 'action'
          },
        },
      ],
      dataSource:[],
      confirmLoading:false,

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
          { required: false, message: '请输入甲方!'},
        ],
        contractFirstAddress:[
          { required: false, message: '请输入甲方地址!'},
        ],
        contractFirstTelphone:[
          { required: false, message: '请输入甲方电话!'},
        ],
        contractFirstFax:[
          { required: false, message: '请输入甲方传真!'},
        ],
        contractFirstContact:[
          { required: false, message: '请输入甲方联系人!'},
        ],
        contractFirstLegalPerson:[
          { required: false, message: '请输入甲方法人!'},
        ],
      },
      refKeys: [],
      url: {
        add: "/srm/contractBase/add",
        draft: "/srm/contractBase/draft",
        edit: "/srm/contractBase/edit",
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
    back(record,childList){
      record.childList = childList;
    },
    handleSplit(record){
      this.$refs.modalForm.add(record);
    },
    handleReject(){
      const that = this
      // 触发表单验证
      that.$refs.form.validate(valid => {
        if (valid) {
          let post = "";
          let url = "";
          post = 'put';
          url = this.url.edit;

          let approveComment = that.model.approveComment;
          if(isNullOrEmpty(approveComment)){
            that.$message.warning("请输入驳回原因");
            return;
          }

          let dataSource2 = that.dataSource2;
          // if(dataSource2 != null && dataSource2.length > 0){
          //   for(let i = 0; i < dataSource2.length; i++){
          //     if(isNullOrEmpty(dataSource2[i].termsName)){
          //       that.$message.warning("第" + (i+1) + "行,条款名称为空");
          //       return;
          //     }
          //     if(isNullOrEmpty(dataSource2[i].termsContent)){
          //       that.$message.warning("第" + (i+1) + "行,条款内容为空");
          //       return;
          //     }
          //   }
          // }
          that.model.contractTermsList = dataSource2;

          let dataSource3 = that.dataSource3;
          if(dataSource3 != null && dataSource3.length > 0){
            for(let i = 0; i < dataSource3.length; i++){
              if(isNullOrEmpty(dataSource3[i].termsName)){
                that.$message.warning("第" + (i+1) + "行,条款名称为空");
                return;
              }
              if(isNullOrEmpty(dataSource3[i].termsContent)){
                that.$message.warning("第" + (i+1) + "行,条款内容为空");
                return;
              }

              if(isNullOrEmpty(dataSource3[i].payMethod)){
                that.$message.warning("第" + (i+1) + "行,支付方式为空");
                return;
              }
              if(isNullOrEmpty(dataSource3[i].payRate)){
                that.$message.warning("第" + (i+1) + "行,支付比例为空");
                return;
              }
              if(isNullOrEmpty(dataSource3[i].payType)){
                that.$message.warning("第" + (i+1) + "行,收款类型为空");
                return;
              }
            }
            that.model.contractPayStepList = dataSource3;
          }

          that.model.contractStatus = "3";
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
    handleOk(){
      const that = this
      // 触发表单验证
      that.$refs.form.validate(valid => {
        if (valid) {
          let post = "";
          let url = "";
          post = 'put';
          url = this.url.edit;
          let dataSource2 = that.dataSource2;
          // if(dataSource2 == null || dataSource2.length == 0){
          //   that.$message.warning("合同条款为空");
          //   return;
          // }
          // for(let i = 0; i < dataSource2.length; i++){
          //   if(isNullOrEmpty(dataSource2[i].termsName)){
          //     that.$message.warning("第" + (i+1) + "行,条款名称为空");
          //     return;
          //   }
          //   if(isNullOrEmpty(dataSource2[i].number)){
          //     that.$message.warning("第" + (i+1) + "行,条款序号为空");
          //     return;
          //   }
          //   if(isNullOrEmpty(dataSource2[i].termsContent)){
          //     that.$message.warning("第" + (i+1) + "行,合同条款为空");
          //     return;
          //   }
          // }
          that.model.contractTermsList = dataSource2;
          let dataSource3 = that.dataSource3;
          if(dataSource3 == null || dataSource3.length == 0){
            that.$message.warning("支付方式为空");
            return;
          }
          for(let i = 0; i < dataSource3.length; i++){
            // if(isNullOrEmpty(dataSource3[i].termsName)){
            //   that.$message.warning("第" + (i+1) + "行,条款名称为空");
            //   return;
            // }
            if(isNullOrEmpty(dataSource3[i].termsContent)){
              that.$message.warning("第" + (i+1) + "行,条款内容为空");
              return;
            }

            if(isNullOrEmpty(dataSource3[i].payMethod)){
              that.$message.warning("第" + (i+1) + "行,支付方式为空");
              return;
            }
            if(isNullOrEmpty(dataSource3[i].payRate)){
              that.$message.warning("第" + (i+1) + "行,支付比例为空");
              return;
            }
            if(isNullOrEmpty(dataSource3[i].payType)){
              that.$message.warning("第" + (i+1) + "行,收款类型为空");
              return;
            }
            that.model.contractPayStepList = dataSource3;
          }
          that.model.contractStatus = "2";
          let dataSource = that.dataSource;
          for(let i = 0; i < dataSource.length; i++){
            if(dataSource[i].childList == null || dataSource[i].childList.length == 0){
              that.$message.warning("第" + (i+1) + "行,标的信息数量未拆分");
              return;
            }
          }


          that.model.contractObjectList = dataSource;
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
    delRow3(record,index){
      this.dataSource3.splice(index,1);
    },
    delRow2(record,index){
      this.dataSource2.splice(index,1);
    },
    addcolumns3(){
      let row = {
        termsName:'',
        payMethod:'',
        termsContent:'',
        payRate:0,
        payType:''
      }
      this.dataSource3.push(row);
    },
    addcolumns2(){
      let row = {
        termsName:'',
        number:'',
        termsContent:'',
        comment:'',
      }
      this.dataSource2.push(row);
    },
    editTwo(record) {
      this.dataSource = [];
      this.dataSource2 = [];
      this.dataSource3 = [];
      this.model = Object.assign({}, record);
      this.model.approver = this.$store.getters.userInfo.realname;
      this.model.approveComment = "";
      this.getContractDetailList(this.model.id);
      this.getProject(this.model.projectId);
      this.getPurchaseRequest(this.model.id);
      this.getPayList(this.model.id);
      this.getTerms(this.model.id);
      // this.getApprove(this.model.id,'contract_supp');
      if("1" == this.model.contractStatus){
        this.getApprove(this.model.id,'contract_zh');
      }
    },
    getApprove(contractId,type){
      let url = "/srm/contractBase/queryApprove";
      getAction(url,{id:contractId,type:type}).then(res => {
        if(res.result != null){
          if(type == 'contract_supp'){
            this.model.approveComment = res.result.approveComment;
          }else{
            this.model.zhApproveComment = res.result.approveComment;
          }
          this.$forceUpdate();
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
        this.dataSource = res.result;
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