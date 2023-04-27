<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container >
      <!-- 主表单区域 -->

      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-tag color="red" style="padding: 5px 10px;font-size: 14px;float: left;margin-top: -10px;margin-bottom: 10px" v-if="model.applyStatus == '20'">
          反馈意见:{{model.resource}}
        </a-tag>
        <a-row>
          <a-divider orientation="left" style="color: #00A0E9">
            基本信息
          </a-divider>

          <a-col :span="8" >
            <a-form-model-item label="收款申请单号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="applyCode">
              <a-input v-model="model.applyCode" placeholder="自动生成" disabled></a-input>
            </a-form-model-item>
          </a-col>

          <a-col :span="8" >
            <a-form-model-item label="合同名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="contractId">
              <j-select-contract ref='contract' v-model="model.contractId" :multi="false" @change="backUser" :disabled="formDisabled"></j-select-contract>
            </a-form-model-item>
          </a-col>

          <a-col :span="8" >
            <a-form-model-item label="合同编码" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="contractNumber">
              <a-input v-model='model.contractNumber' disabled placeholder="合同编码"></a-input>
            </a-form-model-item>
          </a-col>

          <a-col :span="8" >
            <a-form-model-item label="项目名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="projectName">
              <a-input v-model="model.projectName" placeholder="请输入项目名称" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" >
            <a-form-model-item label="收款金额原币" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="payAmountOther">
              <a-input-number v-model="model.payAmountOther" placeholder="请输入收款金额原币" style="width: 100%" disabled
                              :formatter="value => `${value}`.replace(/\B(?=(\d{3})+(?!\d))/g, ',')"
                              :parser="value => value.replace(/\$\s?|(,*)/g, '')"/>
            </a-form-model-item>
          </a-col>

          <a-col :span="8" >
            <a-form-model-item label="收款金额本币" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="payAmount">
              <a-input-number v-model="model.payAmount" placeholder="请输入收款金额" style="width: 100%" disabled
                              :formatter="value => `${value}`.replace(/\B(?=(\d{3})+(?!\d))/g, ',')"
                              :parser="value => value.replace(/\$\s?|(,*)/g, '')"/>
            </a-form-model-item>
          </a-col>

<!--          <a-col :span="8" >-->
<!--            <a-form-model-item label="收款比例(%)" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="payRate">-->
<!--              <a-input-number v-model="model.payRate" placeholder="请输入收款比例" style="width: 100%" @change='changeAmount' :disabled="formDisabled"/>-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->

          <a-col :span="8" >
            <a-form-model-item label="税率" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="taxRate">
<!--              <a-input-number v-model="model.taxRate" placeholder="请输入税率" style="width: 100%" />-->
              <j-dict-select-tag v-model="model.taxRate" placeholder="请输入税率" dictCode="tax_rate" disabled/>
            </a-form-model-item>
          </a-col>

          <a-col :span="8" >
            <a-form-model-item label="收款方式" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="payMethod">
<!--              <a-input v-model="model.payMethod" placeholder="请输入收款方式" ></a-input>-->
              <j-dict-select-tag v-model="model.payMethod" placeholder="请输入收款方式" dictCode="payMethod" :disabled="formDisabled"/>
            </a-form-model-item>
          </a-col>

          <a-col :span="8" >
            <a-form-model-item label="币种" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="currency">
<!--              <a-input v-model="model.currency" placeholder="请输入币种" ></a-input>-->
              <j-dict-select-tag v-model="model.currency" placeholder="请输入币种" dictCode="currency_type" disabled/>
            </a-form-model-item>
          </a-col>

<!--          <a-col :span="8" >-->
<!--            <a-form-model-item label="收款类型" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="payType">-->
<!--              <j-dict-select-tag v-model="model.payType" placeholder="请输入收款类型" dictCode="payType" :disabled="formDisabled"/>-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->

          <a-divider orientation="left" style="color: #00A0E9">
            收款信息
          </a-divider>
          <a-row>
            <a-col :span="8" >
              <a-form-model-item label="供应商名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="suppName">
                <a-input v-model="model.suppName" placeholder="请输入供应商名称" disabled></a-input>
              </a-form-model-item>
            </a-col>

            <a-col :span="8" >
              <a-form-model-item label="收款账号" :labelCol="labelCol" :wrapperCol="wrapperCol" required>
                <a-select v-model="model.receivingNumber" placeholder="请输入收款账号" @change='setBank' :disabled="formDisabled" >
                  <a-select-option
                    v-for="item in accountList"
                    :key="item.bankAccountNum"
                    :value="item.bankAccountNum">
                    {{ item.bankAccountNum }}
                  </a-select-option>
                </a-select>
              </a-form-model-item>
            </a-col>

            <a-col :span="8" >
              <a-form-model-item label="收款开户行" :labelCol="labelCol" :wrapperCol="wrapperCol" required>
                <a-input v-model="model.receivingBank" placeholder="请输入收款开户行" disabled ></a-input>
              </a-form-model-item>
            </a-col>
          </a-row>
          <a-row>
            <a-col :span="8" >
              <a-form-model-item label="备注" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="comment">
                <a-input v-model="model.comment" placeholder="请输入备注" type='textarea' :disabled="formDisabled"></a-input>
              </a-form-model-item>
            </a-col>
          </a-row>

<!--          <a-col :span="8" >-->
<!--            <a-form-model-item label="应收款日期" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="dueDate">-->
<!--              <j-date placeholder="请选择应收款日期" v-model="model.dueDate" style="width: 100%" />-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->



          <a-divider orientation="left" style="color: #00A0E9">
            收款申请明细
          </a-divider>
          <a-button type='primary' @click='batchTime' icon='plus' style='float: right;z-index: 999;margin-left:10px' :disabled="formDisabled">批量设置时间</a-button>
          <a-button type='primary' @click='openDrawer' icon='plus' style='float: right;z-index: 999' :disabled="formDisabled">选择收款设备</a-button>
          <a-table
            ref="table"
            size="middle"
            bordered
            :scroll="{x:1800,y:500}"
            :columns="columns"
            :dataSource="dataSource"
            :pagination="false">

            <template slot='payType' slot-scope='text,record'>
              <j-dict-select-tag v-model="record.payType" placeholder="请输入收款类型" dictCode="payType" :disabled="formDisabled"
                                 :getPopupContainer='getPopupContainer'/>
            </template>

            <template slot='contractAmountTax' slot-scope='text,record'>
              <a-input-number v-model='record.contractAmountTax' :disabled="formDisabled" @change='setAmount' style='width:100%'></a-input-number>
            </template>

            <template slot='no' slot-scope='text,record'>
              <a-input v-model='record.no' :disabled="formDisabled"></a-input>
            </template>

            <template slot='sendTime' slot-scope='text,record'>
              <a-date-picker v-model="record.sendTime" style="width: 100%" :disabled="formDisabled" valueFormat="YYYY-MM-DD"/>
            </template>

            <template slot='planLeadTime' slot-scope='text,record'>
              <a-date-picker v-model="record.planLeadTime" style="width: 100%" :disabled="formDisabled" valueFormat="YYYY-MM-DD"/>
            </template>

            <template slot='action' slot-scope='text,record,index'>
              <a @click='delRow(index)' :disabled="formDisabled">删除</a>
            </template>
          </a-table>

          <a-divider orientation="left" style="color: #00A0E9">
            发票附件
          </a-divider>
          <a-button type='primary' style='float: right;z-index: 999;' @click='handleInvoice' v-if='!formDisabled'>选择发票</a-button>
          <a-table
            ref="table1"
            size="middle"
            bordered
            :scroll="{x:600,y:500}"
            :columns="icColumns"
            :dataSource="icSource"
            :pagination="false">
            <template slot='action' slot-scope='text,record,index'>
              <a @click='delInvoice(record,index)' :disabled="formDisabled">删除</a>
            </template>

            <template slot='attachment' slot-scope='text,record'>
              <j-upload isMultiple  v-model="text" :disabled='true'></j-upload>
            </template>

            <template slot='invoiceAmountTax' slot-scope='text,record'>
              <a-input-number v-model='record.invoiceAmountTax' style='width: 100%'
                              :max='record.maxInvoiceAmountTax - record.hasInvoiceAmountTax'
                              @change='changeInvoiceAmount(record)' :disabled="formDisabled"></a-input-number>
            </template>
          </a-table>

          <a-divider orientation="left" style="color: #00A0E9">
            供应商附件
          </a-divider>
          <a-col :span="8" >
            <a-form-model-item label="" :labelCol="labelCol" :wrapperCol="wrapperCol" style='position: relative'>
              <j-upload isMultiple  v-model="model.suppAttachment" :disabled='formDisabled'></j-upload>
            </a-form-model-item>
          </a-col>

<!--          <a-divider orientation="left" style="color: #00A0E9">-->
<!--            货贷附件-->
<!--          </a-divider>-->
<!--          <a-col :span="8" >-->
<!--            <a-form-model-item label="" :labelCol="labelCol" :wrapperCol="wrapperCol">-->
<!--              <j-upload isMultiple  v-model="model.forwarderAttachment" ></j-upload>-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
        </a-row>
      </a-form-model>
    </j-form-container>
    <contract-object-modal ref='modal' :contractId='model.contractId' @ok='back' ptype='pay'></contract-object-modal>
    <invoice-modal ref='invoiceModal' :contractId='model.contractId' @ok='backInvoice' :applyId='model.id'></invoice-modal>

    <a-drawer
      title="批量设置时间"
      :width="width"
      placement="right"
      :closable="false"
      :headerStyle="{ textAlign: 'center'}"
      @close="close"
      destroyOnClose
      :visible="visible">
      <div>
        <a-form-model ref="ruleFomr" :model="ruleForm">
          <a-col :span="24" >
            <a-form-model-item label="发货时间" :labelCol="labelCol" :wrapperCol="wrapperCol" >
              <a-date-picker v-model="ruleForm.sendTime" style="width: 100%" />
            </a-form-model-item>
          </a-col>
<!--          <a-col :span="24" >-->
<!--            <a-form-model-item label="预估到厂日期" :labelCol="labelCol" :wrapperCol="wrapperCol" >-->
<!--              <a-date-picker v-model="ruleForm.receiveTime" style="width: 100%" />-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
        </a-form-model>
      </div>
      <div class="drawer-footer" style="text-align: center;margin-top:15px;">
        <a-button key="approved" @click="toSubmit" type="primary" v-if="!disableSubmit">确定</a-button>
        <a-button key="cancel" @click="handleClose" style="margin-left:10px;" type="primary" ghost>取消</a-button>
      </div>
    </a-drawer>
  </a-spin>
</template>

<script>

import { JVxeTableModelMixin } from '@/mixins/JVxeTableModelMixin.js'
import JFormContainer from '@/components/jeecg/JFormContainer'
import JSelectContract from '@comp/jeecgbiz/JSelectContract'
import { httpAction, getAction } from '@/api/manage'
import ContractObjectModal from '@views/pay/modules/ContractObjectModal'
import { isNotNullOrEmpty, isNullOrEmpty, preciseNum } from '@/utils/util'
import InvoiceModal from '@views/pay/modules/InvoiceModal'

export default {
    name: 'PurPayApplyForm',
    mixins: [JVxeTableModelMixin],
    components: {
      JFormContainer,
      JSelectContract,
      ContractObjectModal,
      InvoiceModal
    },
    data() {
      return {
        accountList:[],
        ruleForm:{
          sendTime:'',
          receiveTime:'',
        },
        visible:false,
        width:'40%',
        icColumns:[
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
            title: '发票号码',
            dataIndex: 'invoiceNo',
            align:"center",
            width:120,
          },
          {
            title: '开票金额',
            dataIndex: 'maxInvoiceAmountTax',
            align:"center",
            width:120,
          },
          {
            title: '本次使用金额',
            dataIndex: 'invoiceAmountTax',
            align:"center",
            width:120,
            scopedSlots: {customRender: 'invoiceAmountTax'},
          },
          {
            title: '已使用金额',
            dataIndex: 'hasInvoiceAmountTax',
            align:"center",
            width:120,
          },
          {
            title: '发票附件',
            dataIndex: 'attachment',
            align:"center",
            width:140,
            scopedSlots: {customRender: 'attachment'},
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            width:100,
            scopedSlots: {customRender: 'action'},
          },
        ],
        icSource:[

        ],
        dataSource:[],
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
            title: '设备名称',
            dataIndex: 'prodName',
            align:"center",
            width:180,
          },
          {
            title: '规格、型号参数',
            dataIndex: 'prodSpecType',
            align:"center",
            width:120,
            ellipsis:true
          },
          {
            title: '采购数量',
            dataIndex: 'qty',
            align:"center",
            width:120,
          },
          {
            title: '计量单位',
            dataIndex: 'unitId_dictText',
            width:120,
            align:"center",
          },
          {
            title: '发货合同设备序号',
            dataIndex: 'no',
            align:"center",
            width:120,
          },
          {
            title: '收款类型',
            dataIndex: 'payType',
            align:"center",
            width:120,
            scopedSlots: {customRender: 'payType'},
          },
          {
            title: '发货时间',
            dataIndex: 'sendTime',
            align:"center",
            width:120,
            scopedSlots: {customRender: 'sendTime'},
          },
          {
            title: '设备单价(原币)',
            dataIndex: 'priceTax',
            align:"center",
            width:120,
          },
          {
            title: '付款金额(原币)',
            dataIndex: 'contractAmountTax',
            align:"center",
            width:140,
            scopedSlots: {customRender: 'contractAmountTax'},
          },
          {
            title: '付款比例(%)',
            dataIndex: 'payRate',
            align:"center",
            width:120,
          },
          {
            title: '已申请付款金额(原币)',
            dataIndex: 'hasContractAmountTax',
            align:"center",
            width:120,
          },
          {
            title: '操作',
            dataIndex: 'action',
            width:100,
            scopedSlots: {customRender: 'action'},
          },
        ],
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

        validatorRules: {
          contractId: [
            { required: true, message: '请选择合同!',trigger: 'blur'},
          ],
          payRate: [
            { required: true, message: '请输入收款比例!',trigger: 'blur'},
          ],
          payMethod: [
            { required: true, message: '请输入收款方式!',trigger: 'blur'},
          ],
          payType: [
            { required: true, message: '请输入收款类型!',trigger: 'blur'},
          ],
          dueDate:[
            { required: true, message: '请输入应付日期!',trigger: 'blur'},
          ],
          suppAttachment:[
            { required: true, message: '请上传附件!',trigger: 'blur'},
          ],
          receivingBank:[
            { required: true, message: '请输入收款开户行!',trigger: 'change'},
          ],
          receivingNumber:[
            { required: true, message: '请输入收款账号!',trigger: 'change'},
          ]

        },
        url: {
          add: "/srm/purPayApply/add",
          edit: "/srm/purPayApply/edit",
          queryById: "/srm/purPayApply/queryById",
          purPayApplyDetail: {
            list: '/srm/purPayApply/queryPurPayApplyDetailByMainId'
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
      getPopupContainer(node) {
        let element = (() => {
          // nodeType 8	: Comment	: 注释
          if (this.$el && this.$el.nodeType !== 8) {
            return this.$el
          }
          let doc = document.getElementById(this.caseId + 'inputTable')
          if (doc != null) {
            return doc
          }
          return node.parentNode.parentNode.parentNode.parentNode.parentNode.parentNode
        })()

        // 递归判断是否带有 overflow: hidden；的父元素
        const ifParent = (child) => {
          let currentOverflow = null
          if (child['currentStyle']) {
            currentOverflow = child['currentStyle']['overflow']
          } else if (window.getComputedStyle) {
            currentOverflow = window.getComputedStyle(child)['overflow']
          }
          if (currentOverflow != null && currentOverflow != undefined) {
            if (currentOverflow === 'hidden') {
              // 找到了带有 hidden 的标签，判断它的父级是否还有 hidden，直到遇到完全没有 hidden 或 body 的时候才停止递归
              let temp = ifParent(child.parentNode)
              return temp != null ? temp : child.parentNode
            }
            // 当前标签没有 hidden ，如果有父级并且父级不是 body 的话就继续递归判断父级
            else if (child.parentNode && child.parentNode.tagName != undefined && child.parentNode.tagName.toLocaleLowerCase() !== 'body') {
              return ifParent(child.parentNode)
            } else {
              // 直到 body 都没有遇到有 hidden 的标签
              return null
            }
          } else {
            return child
          }
        }

        let temp = ifParent(element)
        return temp != null ? temp : element
      },
      fetchRate(){
        let that = this;
        that.model.exchangeRate = Number(1);
        let url = "/srm/basRateMain/fetchRate";
        let param = {
          currencyB:that.model.currency,
          projectId:that.model.projectId
        }
        getAction(url,param).then(res => {
          that.model.exchangeRate = res.result;
          that.$forceUpdate();
        })
      },
      setBank(){
        let that = this;
        that.model.receivingBank = "";
        if(isNotNullOrEmpty(that.model.receivingNumber) && that.accountList != null && that.accountList.length > 0){
          that.accountList.filter(item => {
            if(item.bankAccountNum == that.model.receivingNumber){
              that.model.receivingBank = item.bankBranch;
              return;
            }
          })
        }
        that.$forceUpdate();
      },
      fetchAccountList(){
        let that = this;
        that.accountList = [];
        let id = that.$store.getters.userInfo.id;
        let url = "/srm/basSupplier/queryBasSupplierBankByMainId";
        getAction(url,{id:id}).then(res => {
          that.accountList = res.result;
        })
      },
      changeInvoiceAmount(record){
        let invoiceAmountTax = record.invoiceAmountTax;

        let exchangeRate = record.exchangeRate;
        let taxRate = record.taxRate;
        if(taxRate == 100){
          taxRate = 0;
        }
        let currency = record.currency;

        if("RMB" == currency){
          let invoiceAmount = Number(preciseNum(Number(invoiceAmountTax) / (Number(taxRate) / 100 + 1),2));
          let invoiceAmountLocal = invoiceAmount;
          let invoiceAmountTaxLocal = invoiceAmountTax;

          record.invoiceAmount = invoiceAmount;
          record.invoiceAmountLocal = invoiceAmountLocal;
          record.invoiceAmountTaxLocal = invoiceAmountTaxLocal;
        }else{
          let invoiceAmount = invoiceAmountTax;
          let invoiceAmountTaxLocal = Number(preciseNum(Number(invoiceAmountTax) / Number(exchangeRate),2));
          let invoiceAmountLocal = invoiceAmountTaxLocal;

          record.invoiceAmount = invoiceAmount;
          record.invoiceAmountLocal = invoiceAmountLocal;
          record.invoiceAmountTaxLocal = invoiceAmountTaxLocal;
        }

      },
      delInvoice(record,index){
        this.icSource.splice(index,1);
      },
      toSubmit(){
        if(this.dataSource == null || this.dataSource.length == 0){
          this.$message.error("请选择收款明细后在设置发货日期、预估到厂日期");
          return;
        }
        if(isNullOrEmpty(this.ruleForm.sendTime)){
          this.$message.error("请输入发货日期");
          return;
        }
        // if(isNullOrEmpty(this.ruleForm.receiveTime)){
        //   this.$message.error("请输入预估到厂日期");
        //   return;
        // }
        this.dataSource.filter(item => {
          item.sendTime = this.ruleForm.sendTime;
          item.planLeadTime = this.ruleForm.receiveTime;
        })
        this.handleClose();
      },
      handleClose(){
        this.visible = false;
      },
      backInvoice(rows){
        // let attachmentList = [];
        // let ids = [];
        // let suppAttachment = this.model.suppAttachment;
        // let invoiceIds = this.model.invoiceIds;
        // if(isNotNullOrEmpty(suppAttachment)){
        //   attachmentList = suppAttachment.split(',');
        // }
        // if(isNotNullOrEmpty(invoiceIds)){
        //   ids = invoiceIds.split(",");
        // }
        // rows.filter(item => {
        //   if(attachmentList.indexOf(item.attachment) == -1){
        //     attachmentList.push(item.attachment);
        //   }
        //   if(ids.indexOf(item.id) == -1){
        //     ids.push(item.id);
        //   }
        // });
        // this.model.suppAttachment = attachmentList.join(",");
        // this.model.invoiceIds = ids.join(",");
        // this.$forceUpdate();
        // this.$message.success("添加成功");
        let that = this;
        rows.filter(item => {
          let flag = false;
          that.icSource.filter(rw => {
            if(item.id == rw.invoiceId){
              flag = true;
            }
          })
          if(!flag){
            let invoice = {

              invoiceId:item.id,
              invoiceAmount:Number(item.invoiceAmount) - Number(item.hasInvoiceAmount),
              invoiceAmountTax:Number(item.invoiceAmountTax) - Number(item.hasInvoiceAmountTax),
              invoiceAmountLocal:Number(item.invoiceAmountLocal) - Number(item.hasInvoiceAmountLocal),
              invoiceAmountTaxLocal:Number(item.invoiceAmountTaxLocal) - Number(item.hasInvoiceAmountTaxLocal),

              hasInvoiceAmount:Number(item.hasInvoiceAmount),
              hasInvoiceAmountTax:Number(item.hasInvoiceAmountTax),
              hasInvoiceAmountLocal:Number(item.hasInvoiceAmountLocal),
              hasInvoiceAmountTaxLocal:Number(item.hasInvoiceAmountTaxLocal),

              maxInvoiceAmountTax : Number(item.invoiceAmountTax),

              attachment:item.attachment,
              invoiceNo:item.invoiceNo,
              exchangeRate:item.exchangeRate,
              taxRate:item.taxRate,
              currency:item.currency
            }
            that.icSource.push(invoice);
          }
        })
        this.$message.success("添加成功");
      },
      handleInvoice(){
        let contractId = this.model.contractId;
        if(isNullOrEmpty(contractId)){
          this.$message.error("请选择合同");
          return;
        }
        this.$refs.invoiceModal.loadData(1);
      },
      changeAmount(){
        this.setAmount();
      },
      handleDraft() {
        const that = this;
        // 触发表单验证
        that.$refs.form.validate(valid => {
          if (true) {

            let httpurl = "/srm/purPayApply/draft";
            let method = 'post';

            let dataSource = this.dataSource;
            that.model.purPayApplyDetailList = dataSource;
            that.model.applyInvoiceList = that.icSource;
            that.$confirm({
              content: `是否确认提交?`,
              onOk: () => {
                that.confirmLoading = true;
                httpAction(httpurl, that.model, method).then((res) => {
                  if (res.success) {
                    that.$message.success(res.message);
                    that.$emit('ok');
                  } else {
                    that.$message.warning(res.message);
                  }
                }).finally(() => {
                  that.confirmLoading = false;
                })
              }
            })
          }
        })
      },
      handleOk() {
        const that = this;
        // 触发表单验证
        that.$refs.form.validate(valid => {
          if (valid) {
            let httpurl = '';
            let method = '';
            if (!that.model.id) {
              httpurl += that.url.add;
              method = 'post';
            } else {
              httpurl += that.url.edit;
              method = 'put';
            }

            let receivingNumber = that.model.receivingNumber;
            if(isNullOrEmpty(receivingNumber)){
              that.$message.error("请输入收款账号");
              return false;
            }
            let receivingBank = that.model.receivingBank;
            if(isNullOrEmpty(receivingBank)){
              that.$message.error("请输入收款开户行");
              return false;
            }

            let dataSource = that.dataSource;
            let msg = "";
            if(dataSource == null || dataSource.length == 0){
              that.$message.error("请选择设备");
              return false;
            }
            for(let i = 0 ;i < dataSource.length; i++){
              // if(isNullOrEmpty(dataSource[i].no)){
              //   that.$message.error("第" + (i+1) + "行,请输入发货合同设备序号");
              //   return false;
              // }
              if(isNullOrEmpty(dataSource[i].payType)){
                that.$message.error("第" + (i+1) + "行,请选择付款类型");
                return false;
              }
              if(isNullOrEmpty(dataSource[i].sendTime)){
                that.$message.error("第" + (i+1) + "行,请输入发货时间");
                return false;
              }
              if(isNullOrEmpty(dataSource[i].contractAmountTax)){
                that.$message.error("第" + (i+1) + "行,请输入付款金额");
                return false;
              }
              // let totalAmount = Number(dataSource[i].contractAmountTax) + Number(dataSource[i].hasContractAmountTax);
              // if(totalAmount > dataSource[i].priceTax){
              //   msg= msg + "第" + (i+1) + "行,累计付款金额大于合同金额;";
              // }
            }
            that.model.purPayApplyDetailList = dataSource;
            that.model.applyInvoiceList = that.icSource;
            console.log(dataSource);

            that.$confirm({
              content: msg+`是否确认提交?`,
              onOk: () => {
                that.confirmLoading = true;
                httpAction(httpurl, that.model, method).then((res) => {
                  if (res.success) {
                    that.$message.success(res.message);
                    that.$emit('ok');
                  } else {
                    that.$message.warning(res.message);
                  }
                }).finally(() => {
                  that.confirmLoading = false;
                })
              }
            })
          }
        })
      },

      delRow(index){
        this.dataSource.splice(index,1);
        setTimeout(() => {
          this.setAmount();
        }, 300)
      },

      back(rows){
        //拆成一个数量一行数据
        rows.filter(item => {
          let flag = false;
          this.dataSource.filter(child => {
            if(item.id == child.id){
              flag = true;
              return;
            }
          })
          if(!flag){
            let record = JSON.parse(JSON.stringify(item));
            record.billDetailId = record.id;
            record.no = record.sort;
            record.priceTax = record.contractPriceTax;
            record.contractAmountTax = 0;
            record.payRate = 0;
            record.hasContractAmountTax = item.payRate;
            record.payApplyAmount = 0;
            this.dataSource.push(record);
          }
        })
        this.$message.success("添加成功");
        // setTimeout(() => {
        //   this.setAmount();
        // }, 500)
      },
      setAmount(){
        let that = this;
        let payAmount = 0;
        let payAmountOther = 0;
        let payRate = 0;
        //获取最新的汇率
        let exchangeRate = that.model.exchangeRate;
        if(that.model.currency == "RMB"){
          exchangeRate = 1;
        }
        // let payRate = this.model.payRate;
        // this.dataSource.filter(item => {
        //   payAmount = Number(payAmount) + Number(item.contractAmountTaxLocal);
        //   payAmountOther = Number(payAmountOther) + Number(item.contractAmountTax)
        // })
        // if(isNotNullOrEmpty(payRate)){
        //   payAmount = Number(preciseNum(payAmount,2)) * (Number(payRate) / 100);
        //   payAmountOther = Number(preciseNum(payAmountOther,2)) * (Number(payRate) / 100);
        // }
        // this.model.payAmount = Number(preciseNum(payAmount,2));
        // this.model.payAmountOther = Number(preciseNum(payAmountOther,2));
        // this.$forceUpdate();
        let totalAmountTax = 0;

        that.dataSource.filter(item => {
          if(isNotNullOrEmpty(item.contractAmountTax)){
            payAmountOther = Number(payAmountOther) + Number(item.contractAmountTax);
            item.contractPriceTax = item.contractAmountTax;
            item.payRate = Number(preciseNum(Number(item.contractAmountTax) / Number(item.priceTax) * 100,0));
            item.contractAmountTaxLocal = Number(preciseNum(Number(item.contractAmountTax) / Number(exchangeRate),2));
            item.contractPriceTaxLocal = item.contractAmountTaxLocal;
            // payRate = Number(payRate) + item.payRate;
            totalAmountTax = Number(totalAmountTax) + Number(item.priceTax);
          }
        })
        payAmount = Number(preciseNum(Number(payAmountOther) / Number(exchangeRate),2));
        this.model.payAmount = payAmount;
        this.model.payAmountOther = Number(preciseNum(payAmountOther,2));
        this.model.payRate = Number(preciseNum(Number(payAmountOther) / Number(totalAmountTax) * 100,0));
        that.$forceUpdate();
      },
      backUser(ids,rows){
        this.model.contractName = rows[0].contractName;
        this.model.contractNumber = rows[0].contractNumber;
        this.model.taxRate = rows[0].contractTaxRate;
        this.model.currency = rows[0].contractCurrency;
        this.model.receivingBank = rows[0].contractSecondOpeningBank;
        this.model.receivingNumber = rows[0].contractSecondBankAccount;
        this.model.projectId = rows[0].projectId;
        this.getProjectById(rows[0].projectId);
        this.fetchRate();
        this.$forceUpdate();
      },
      getProjectById(id){
        let url = "/srm/projBase/queryById";
        let param = {
          id:id
        }
        getAction(url,param).then(res => {
          this.model.projectId = res.result.id;
          this.model.projectName = res.result.projName;
          this.$forceUpdate();
        })
      },
      batchTime(){
        this.visible = true;
        this.ruleForm.sendTime = '';
        this.ruleForm.receiveTime = '';
      },
      openDrawer(){
        let contractId = this.model.contractId;
        if(isNullOrEmpty(contractId)){
          this.$message.error("请选择合同");
          return;
        }
        this.$refs.modal.loadData();
      },
      add () {
        this.edit(this.modelDefault);
      },
      edit(record) {
        this.dataSource = [];
        this.model = Object.assign({}, record);
        this.model.suppName = this.$store.getters.userInfo.realname;
        this.fetchAccountList();
        if(this.model.id){
          this.fetchDetailList(this.model.id);
          this.getApprove(this.model.id);
          this.fetchInvoiceList(this.model.id);
        }
      },
      fetchInvoiceList(id){
        let url = "/srm/purchaseApplyInvoice/queryInvoiceList";
        let param = {
          id:id
        }
        getAction(url,param).then(res => {
          this.icSource = res.result;
        })
      },
      getApprove(id){
        let url = "/srm/contractBase/queryApprove";
        let param = {
          id:id
        }
        getAction(url,param).then(res => {
          this.model.resource = res.result.approveComment;
          this.$forceUpdate();
        })
      },
      fetchDetailList(applyId){
        let url = "/srm/purPayApply/queryPurPayApplyDetailByMainId";
        let param = {
          id:applyId
        }
        getAction(url,param).then(res => {
          this.dataSource = res.result;
        })
      },
    }
  }
</script>

<style scoped>
</style>