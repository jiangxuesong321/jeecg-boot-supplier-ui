<template>
	<a-spin :spinning="confirmLoading">
		<j-form-container >
			<!-- 主表单区域 -->
			<a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="8">
            <a-form-model-item label="询价单号" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
              <a-input v-model="model.inquiryCode" placeholder="自动生成" disabled></a-input>
            </a-form-model-item>
          </a-col>

          <a-col :span="8">
            <a-form-model-item label="询报价名称" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
                               prop="inquiryName">
              <a-input v-model="model.inquiryName" placeholder="询报价名称" disabled></a-input>
            </a-form-model-item>
          </a-col>

          <a-col :span="8">
            <a-form-model-item label="采购员" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
                               prop="inquirer">
              <j-dict-select-tag v-model="model.inquirer" placeholder="请输入采购员" dictCode="sys_user,realname,username" disabled/>
            </a-form-model-item>
          </a-col>

<!--          <a-col :span="8">-->
<!--            <a-form-model-item label="币种" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"-->
<!--                               prop="currency">-->
<!--              <j-dict-select-tag v-model="model.currency" placeholder="请输入币种" dictCode="currency_type" disabled/>-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->

          <a-col :span="8">
            <a-form-model-item label="报价截止日期" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
                               prop="quotationDeadline">
              <j-date placeholder="请选择报价截止日期" v-model="model.quotationDeadline" style="width: 100%" disabled/>
            </a-form-model-item>
          </a-col>
        </a-row>
        <a-row>
          <a-col :span="12">
            <a-form-model-item label="采购说明" :labelCol="spans.labelCol2" :wrapperCol="spans.wrapperCol2">
              <a-input type='textarea' v-model='model.remark' disabled style='width: 60%'></a-input>
            </a-form-model-item>
          </a-col>
        </a-row>
        <a-row>
          <a-col :span="12">
            <a-form-model-item label="详细规格需求说明" :labelCol="spans.labelCol2" :wrapperCol="spans.wrapperCol2">
              <a-input type='textarea' v-model='model.reqComment' disabled style='width: 60%'></a-input>
            </a-form-model-item>
          </a-col>
        </a-row>
<!--        <a-row>-->
<!--          <a-col :span="12">-->
<!--            <a-form-model-item label="投标须知" :labelCol="spans.labelCol2" :wrapperCol="spans.wrapperCol2">-->
<!--              <a-input type='textarea' v-model='model.zbComment' disabled></a-input>-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
<!--        </a-row>-->
        <a-row>
          <a-col :span="12">
            <a-form-model-item label="招标附件" :labelCol="spans.labelCol2" :wrapperCol="spans.labelCol2" >
              <j-upload isMultiple v-model="model.zbAttachment" :disabled="true"></j-upload>
            </a-form-model-item>
          </a-col>
        </a-row>

				<a-divider orientation="left" style="color: #00A0E9">
					标的信息
				</a-divider>
        <a-col :span="8" v-if="ptype == 'bargain'">
          <a-form-model-item label="当前议价价格" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
            <a-input placeholder="当前议价价格" v-model="model.bgOrderPriceTax" style="width: 100%" disabled/>
          </a-form-model-item>
        </a-col>
<!--        <a-col :span="8" v-if="ptype == 'bargain'">-->
<!--          <a-form-model-item label="当前议价运费" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">-->
<!--            <a-input placeholder="当前议价运费" v-model="model.bgFareAmount" style="width: 100%" disabled/>-->
<!--          </a-form-model-item>-->
<!--        </a-col>-->

        <a-button type='primary' style='float:right;z-index: 999' @click='addChild' v-if="ptype != 'bargain'" :disabled="formDisabled">添加明细</a-button>
				<a-table
				  ref="table"
				  size="small"
				  rowKey="id"
				  :scroll="{x:true, y:350}"
				  :columns="model.reqType == '0' ? columns:columns1"
				  :dataSource="dataSource"
				  :pagination="false"
          @expand="open"
          :expandedRowKeys="expandedRowKeys">

          <div slot="expandedRowRender" slot-scope="text">
            <div class="rfq-content rfq-content2" style="width:400px">
              <table>
                <thead>
                <tr>
                  <th style="width: 80px">序号</th>
                  <th style="width: 200px">配套产品</th>
                  <th style="width: 200px">品牌</th>
                  <th style="width: 200px">规格型号</th>
                  <th style="width: 180px">税率</th>
                  <th style="width: 180px">数量</th>
                  <th style="width: 180px">单价</th>
                  <th style="width: 180px">总价</th>
                  <th style="width: 100px">操作</th>
                </tr>
                </thead>
                <tbody>
                <tr v-for="(item, index) in text.childList">
                  <td style="width: 80px">{{index + 1}}</td>
                  <td style="width: 200px">
                    <a-input v-model='item.prodName' placeholder="配套设备" @change='setVal(item)' :disabled="formDisabled"></a-input>
                  </td>
                  <td style="width: 200px">
                    <a-input v-model='item.brandName' placeholder="品牌" @change='setVal(item)' :disabled="formDisabled"></a-input>
                  </td>
                  <td style="width: 200px">
                    <a-input v-model='item.speType' placeholder="规格型号" @change='setVal(item)' :disabled="formDisabled"></a-input>
                  </td>
                  <td style="width: 180px">
                    <j-dict-select-tag placeholder="请输入税率" v-model="item.taxRate" dictCode="tax_rate" :getPopupContainer='getPopupContainer' :disabled="formDisabled"/>
                  </td>
                  <td style="width: 180px">
                    <a-input-number v-model='item.qty' placeholder="数量" style='width: 100%' @change='setVal(item)' :disabled="formDisabled"></a-input-number>
                  </td>
                  <td style="width: 180px">
                    <a-input-number v-model='item.orderPriceTax' placeholder="单价" style='width: 100%' @change='setVal(item)' :disabled="formDisabled"
                                    :formatter="value => `${value}`.replace(/\B(?=(\d{3})+(?!\d))/g, ',')"
                                    :parser="value => value.replace(/\$\s?|(,*)/g, '')"></a-input-number>
                  </td>

                  <td style="width: 180px">
<!--                      {{item.orderAmountTax}}-->
                    <a-input-number v-model='item.orderAmountTax' placeholder="总价" style='width: 100%' disabled
                                    :formatter="value => `${value}`.replace(/\B(?=(\d{3})+(?!\d))/g, ',')"
                                    :parser="value => value.replace(/\$\s?|(,*)/g, '')"></a-input-number>
                  </td>
                  <td style="width: 100px">
                    <a @click='delRow(index,text.childList)' :disabled="formDisabled">删除</a>
                  </td>
                </tr>
                </tbody>
              </table>
            </div>
          </div>


				  <template slot='suppBrandName' slot-scope='text,record'>
            <a-input v-model='record.suppBrandName' :disabled="formDisabled"></a-input>
          </template>

          <template slot='suppSpeType' slot-scope='text,record'>
            <a-input v-model='record.suppSpeType' :disabled="formDisabled"></a-input>
          </template>

          <template slot='suppCurrency' slot-scope='text,record'>
            <j-dict-select-tag placeholder="请输入币种" v-model="record.suppCurrency" dictCode="currency_type" :getPopupContainer='getPopupContainer' :disabled="formDisabled"/>
          </template>

          <template slot='orderPriceTax' slot-scope='text,record' >
            <a-input-number v-model='record.orderPriceTax' style='width: 100%' @change='setAmount(record)' :disabled="formDisabled" v-if="ptype != 'bargain'"
                            :formatter="value => `${value}`.replace(/\B(?=(\d{3})+(?!\d))/g, ',')"
                            :parser="value => value.replace(/\$\s?|(,*)/g, '')"></a-input-number>
            <a-input-number v-model='record.orderPriceTax' style='width: 100%' @change='setAmount(record)'  v-if="ptype == 'bargain'"
                            :formatter="value => `${value}`.replace(/\B(?=(\d{3})+(?!\d))/g, ',')"
                            :parser="value => value.replace(/\$\s?|(,*)/g, '')"></a-input-number>
          </template>

          <template slot='orderAmountTax' slot-scope='text,record' >
            <a-input-number v-model='record.orderAmountTax' style='width: 100%'  disabled
                            :formatter="value => `${value}`.replace(/\B(?=(\d{3})+(?!\d))/g, ',')"
                            :parser="value => value.replace(/\$\s?|(,*)/g, '')"></a-input-number>
          </template>

          <template slot='fareAmount' slot-scope='text,record' >
            <a-input-number v-model='record.fareAmount' style='width: 100%' :disabled="formDisabled" v-if="ptype != 'bargain'"></a-input-number>
            <a-input-number v-model='record.fareAmount' style='width: 100%' v-if="ptype == 'bargain'"></a-input-number>
          </template>

          <template slot='taxRate' slot-scope='text,record'>
            <j-dict-select-tag placeholder="请输入税率" v-model="record.taxRate" dictCode="tax_rate" :getPopupContainer='getPopupContainer' :disabled="formDisabled"/>
          </template>

          <template slot='tradeType' slot-scope='text,record'>
            <j-dict-select-tag placeholder="请输入贸易方式" v-model="record.tradeType"
                               v-if='record.suppCurrency != "RMB" && record.suppCurrency != null'
                               dictCode="trade_type" :getPopupContainer='getPopupContainer' :disabled="formDisabled"/>
            <span v-else>
              -
            </span>
          </template>

          <template slot='suppLeadTime' slot-scope='text,record'>
<!--            <a-date-picker v-model="record.suppLeadTime" style="width: 100%" :disabled="formDisabled" :disabled-date="disabledDate"/>-->

            <a-date-picker v-model="record.suppLeadTime" style="width: 100%" :disabled="formDisabled" :disabled-date="disabledDate1"
                           v-if='record.childList != null && record.childList.length > 1' placeholder="交期"/>
            <a-date-picker v-model="record.suppLeadTime" style="width: 100%" :disabled="formDisabled" :disabled-date="disabledDate"
                           v-else placeholder="交期"/>
          </template>

        </a-table>
				<a-card class="card" title="报价说明和附件" :bordered="false" style="margin-top: 15px;" v-if='ptype != "bargain"'>
					<a-row>
						<a-col :span="24">
							<a-form-item label="报价说明" :labelCol="spans.labelCol1" :wrapperCol="spans.wrapperCol1">
								<a-input v-model="model.comment" placeholder="请输入报价说明" :rows="3" type='textarea' :disabled="formDisabled" style='width: 27%'></a-input>
							</a-form-item>
						</a-col>
					</a-row>
					<a-row style='margin-left: -10%'>
						<a-col :span="24">
							<a-form-item label="报价及配置规格文件" :labelCol="labelCol1" :wrapperCol="wrapperCol1" required>
								<j-upload v-model="model.attachment" :trigger-change="true" :disabled="formDisabled"></j-upload>
							</a-form-item>
						</a-col>
					</a-row>
				</a-card>
        <a-card class="card" title="议价附件" :bordered="false" style="margin-top: 15px;" v-if='ptype == "bargain"'>
          <a-col :span="24">
            <a-form-item label="报价及配置规格文件" :labelCol="spans.labelCol1" :wrapperCol="spans.wrapperCol1" required>
              <j-upload v-model="model.attachment" :trigger-change="true" ></j-upload>
            </a-form-item>
          </a-col>
        </a-card>
			</a-form-model>
		</j-form-container>
	</a-spin>
</template>

<script>
import { JVxeTableModelMixin } from '@/mixins/JVxeTableModelMixin.js'
import JFormContainer from '@/components/jeecg/JFormContainer'
import { billListMixin } from '../../mixins/billListMixin'
import { billModalMixin } from '../../mixins/billModalMixin'
import { httpAction, getAction, putAction } from '@/api/manage'
import { isNotNullOrEmpty, isNullOrEmpty, preciseNum } from '@/utils/util'
import moment from 'moment';

export default {
		name: 'BiddingMainForm',
		mixins: [JVxeTableModelMixin,billListMixin, billModalMixin],
		components: {
			JFormContainer,
		},
		data() {
			return {
        expandedRowKeys:[],
			  ptype:'',
				labelCol: {
					xs: {
						span: 24
					},
					sm: {
						span: 5
					},
				},
				wrapperCol: {
					xs: {
						span: 24
					},
					sm: {
						span: 16
					},
				},
        labelCol1: {span: 4},
        wrapperCol1: {span: 20},
				model: {},
				// 新增时子表默认添加几行空数据
				addDefaultRowNum: 1,
				validatorRules: {},
        columns1: [
          {
            title: '设备名称',
            align: "center",
            dataIndex: 'prodName',
            width: 160,
          },
          // {
          //   title: '规格型号',
          //   align: "center",
          //   dataIndex: 'speType',
          //   width: 120,
          // },
          {
            title: '需求数量',
            align: "center",
            dataIndex: 'qty',
            customRender: function(t, r, index) {
              if(r.unitId_dictText != null && r.unitId_dictText != '' && r.unitId_dictText != undefined){
                return r.qty + r.unitId_dictText;
              }else{
                return r.qty;
              }
            },
            width: 100,
          },
          {
            title: '期望交期',
            align: "center",
            dataIndex: 'leadTime',
            width: 120,
          },
          // {
          //   title: '报价品牌',
          //   align: "center",
          //   dataIndex: 'suppBrandName',
          //   width: 140,
          //   scopedSlots: {customRender: 'suppBrandName'}
          // },
          // {
          //   title: '报价规格型号',
          //   align: "center",
          //   dataIndex: 'suppSpeType',
          //   width: 140,
          //   scopedSlots: {customRender: 'suppSpeType'}
          // },
          {
            title: '币种',
            align: "center",
            dataIndex: 'suppCurrency',
            width: 120,
            scopedSlots: {customRender: 'suppCurrency'}
          },
          {
            title: '税率(%)',
            align: "center",
            dataIndex: 'taxRate',
            width: 120,
            scopedSlots: {customRender: 'taxRate'}
          },
          {
            title: '报价单价',
            align: "center",
            dataIndex: 'orderPriceTax',
            width: 120,
            scopedSlots: {customRender: 'orderPriceTax'}
          },
          {
            title: '报价金额',
            align: "center",
            dataIndex: 'orderAmountTax',
            width: 120,
            scopedSlots: {customRender: 'orderAmountTax'}
          },
          // {
          //   title: '运费',
          //   align: "center",
          //   dataIndex: 'fareAmount',
          //   width: 120,
          //   scopedSlots: {customRender: 'fareAmount'}
          // },
          {
            title: '贸易方式',
            align: "center",
            dataIndex: 'tradeType',
            width: 140,
            scopedSlots: {customRender: 'tradeType'}
          },
          {
            title: '预估交期',
            align: "center",
            dataIndex: 'suppLeadTime',
            width: 120,
            scopedSlots: {customRender: 'suppLeadTime'}
          }
        ],
				columns: [
					{
						title: '设备名称',
						align: "center",
						dataIndex: 'prodName',
						width: 160,
					},
					{
						title: '规格型号',
						align: "center",
						dataIndex: 'speType',
						width: 120,
					},
					{
						title: '需求数量',
						align: "center",
						dataIndex: 'qty',
            customRender: function(t, r, index) {
						  if(r.unitId_dictText != null && r.unitId_dictText != '' && r.unitId_dictText != undefined){
                return r.qty + r.unitId_dictText;
              }else{
                return r.qty;
              }
            },
						width: 100,
					},
					{
						title: '期望交期',
						align: "center",
						dataIndex: 'leadTime',
						width: 120,
					},
          {
            title: '报价品牌',
            align: "center",
            dataIndex: 'suppBrandName',
            width: 140,
            scopedSlots: {customRender: 'suppBrandName'}
          },
          {
            title: '报价规格型号',
            align: "center",
            dataIndex: 'suppSpeType',
            width: 140,
            scopedSlots: {customRender: 'suppSpeType'}
          },
          {
            title: '币种',
            align: "center",
            dataIndex: 'suppCurrency',
            width: 120,
            scopedSlots: {customRender: 'suppCurrency'}
          },
          {
            title: '税率(%)',
            align: "center",
            dataIndex: 'taxRate',
            width: 120,
            scopedSlots: {customRender: 'taxRate'}
          },
          {
            title: '报价单价',
            align: "center",
            dataIndex: 'orderPriceTax',
            width: 120,
            scopedSlots: {customRender: 'orderPriceTax'}
          },
          {
            title: '报价金额',
            align: "center",
            dataIndex: 'orderAmountTax',
            width: 120,
            scopedSlots: {customRender: 'orderAmountTax'}
          },
          // {
          //   title: '运费',
          //   align: "center",
          //   dataIndex: 'fareAmount',
          //   width: 120,
          //   scopedSlots: {customRender: 'fareAmount'}
          // },
          {
            title: '贸易方式',
            align: "center",
            dataIndex: 'tradeType',
            width: 140,
            scopedSlots: {customRender: 'tradeType'}
          },
          {
            title: '预估交期',
            align: "center",
            dataIndex: 'suppLeadTime',
            width: 140,
            scopedSlots: {customRender: 'suppLeadTime'}
          }
				],
				dataSource: [
				],
				url: {
					add: "/srm/supQuote/add",
				},
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
			formDisabled() {
				return this.disabled
			},
		},
		created() {},
		methods: {
      moment,
      disabledDate1(current) {
        // Can not select days before today and today
        return current && current < moment().subtract(1, 'days').endOf('day')
      },
      disabledDate(current) {
        // Can not select days before today and today
        return current && current < moment().endOf('day');
      },
      delRow(index,childList){
        let that = this;
        that.$confirm({
          title:"确认操作",
          content:"是否确认删除?",
          onOk: function(){
            childList.splice(index,1);
          }
        });
      },
      setVal(item){
        let qty = item.qty;
        let orderPriceTax = item.orderPriceTax;
        let fareAmount = item.fareAmount;
        if(isNullOrEmpty(fareAmount)){
          fareAmount = 0;
        }
        if(isNotNullOrEmpty(qty) && isNotNullOrEmpty(orderPriceTax)){
          let orderAmountTax = preciseNum(Number(qty) * Number(orderPriceTax) + Number(fareAmount),2);
          item.orderAmountTax = Number(orderAmountTax);
        }
        this.$forceUpdate();
      },
      addChild(){
        let row = this.dataSource[0];
        this.expandedRowKeys = []
        this.expandedRowKeys.push(row.id);
        let child = {
          prodName:'',
          brandName:'',
          speType:'',
          qty:0,
          orderPriceTax:'',
          orderAmountTax:'',
          taxRate : row.taxRate
        }
        if(row.childList == null || row.childList.length == 0){
          row.childList = [];
        }
        row.childList.push(child);
        this.$forceUpdate();
      },
      open(expanded, row) {
        if (expanded) {
          this.expandedRowKeys = []
          this.expandedRowKeys.push(row.id)
        } else {
          this.expandedRowKeys = []
        }
      },
      setAmount(record){
        let orderAmountTax = preciseNum(Number(record.qty) * Number(record.orderPriceTax),2);
        record.orderAmountTax = Number(orderAmountTax);
        let childList = record.childList;
        if(childList != null && childList.length == 1){
          childList.filter(item => {
            item.orderPriceTax = record.orderPriceTax;
            item.orderAmountTax = item.orderPriceTax * item.qty;
            this.$forceUpdate();
          })
        }
      },
      handleOk(){
        let that = this;
        let ruleForm = JSON.parse(JSON.stringify(that.model));
        ruleForm.recordList = that.dataSource;
        for(let i = 0; i < that.dataSource.length; i++){
          let row = that.dataSource[i];
          if(isNullOrEmpty(row.suppCurrency)){
            that.$message.error("请选择报价币种");
            return;
          }
          if(isNullOrEmpty(row.tradeType) && row.suppCurrency != 'RMB'){
            that.$message.error("请选择贸易方式");
            return;
          }
          if(isNullOrEmpty(row.orderPriceTax)){
            that.$message.error("请输入报价单价");
            return;
          }
          if(isNullOrEmpty(row.taxRate)){
            that.$message.error("请输入税率");
            return;
          }
          if(isNullOrEmpty(row.suppLeadTime)){
            that.$message.error("请输入预估交期");
            return;
          }
          let amountTax = row.orderAmountTax;
          let cAmountTax = 0;
          let childList = row.childList;
          if(childList != null && childList.length > 0){
            for(let j = 0; j < childList.length; j++){
              if(isNullOrEmpty(childList[j].prodName)){
                that.$message.error("子项产品为空");
                return;
              }
              if(isNullOrEmpty(childList[j].qty)){
                that.$message.error("子项产品数量为空");
                return;
              }
              if(isNullOrEmpty(childList[j].orderPriceTax)){
                that.$message.error("子项产品单价为空");
                return;
              }
              if(isNullOrEmpty(childList[j].taxRate)){
                that.$message.error("子项产品税率为空");
                return;
              }
              if(childList[j].taxRate == 100){
                that.$message.error("子项产品税率不能为其他");
                return;
              }
              cAmountTax = Number(cAmountTax) + Number(childList[j].orderAmountTax);
            }
            if(cAmountTax != amountTax){
              that.$message.error("子项产品金额能与明细行金额不相等");
              return;
            }
          }
        }
        let attachment = ruleForm.attachment;
        if(isNullOrEmpty(attachment)){
          that.$message.error("请上传附件");
          return;
        }
        that.$confirm({
          content: `是否确认提交`,
          onOk: () => {
            let url = "";
            if(that.ptype == 'bargain'){
              url = "/srm/supBargain/edit";
            }else{
              url = "/srm/supQuote/add";
            }
            let post = "post";
            httpAction(url,ruleForm,post).then((res)=> {
              if (res.success) {
                that.$message.success(res.message);
                that.$emit('ok');
              } else {
                that.$message.warning(res.message);
              }
            })
          }
        })
      },
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
          if (currentOverflow != null) {
            if (currentOverflow === 'hidden') {
              // 找到了带有 hidden 的标签，判断它的父级是否还有 hidden，直到遇到完全没有 hidden 或 body 的时候才停止递归
              let temp = ifParent(child.parentNode)
              return temp != null ? temp : child.parentNode
            }
            // 当前标签没有 hidden ，如果有父级并且父级不是 body 的话就继续递归判断父级
            else if (child.parentNode && child.parentNode.tagName.toLocaleLowerCase() !== 'body') {
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
      bargain(record){
        this.disabled = true;
        this.ptype = 'bargain';
        this.dataSource = [];
        this.model = record;
        this.fetchQuoteList(record);

      },
      editTwo(record){
        this.ptype = 'quote';
        this.dataSource = [];
        this.model = record;
        this.fetchQuoteList(record);
      },
      detail(record){
        this.ptype = 'view';
        this.dataSource = [];
        this.model = record;
        this.fetchQuoteList(record);
      },
      edit(record) {
        this.ptype = 'quote';
        this.dataSource = [];
        this.model = record;
        // this.model.zbAttachment = record.attachment;
        this.model.reqComment = record.reqComment;
        this.model.zbComment = record.comment;

        this.model.comment = "";
        this.model.attachment = "";

        this.dataSource.push(record);
      },
      fetchQuoteList(record){
        let url = "/srm/supQuote/queryQuoteByRecordId";
        let param = {
          recordId:record.id
        }
        getAction(url,param).then(res => {
          let row = res.result;
          if(row != null){
            record.suppBrandName = row.brandName;
            record.suppSpeType = row.speType;
            record.suppCurrency = row.currency;
            record.suppLeadTime = row.leadTime;
            record.taxRate = row.taxRate;
            record.fareAmount = row.fareAmount;
            record.bgFareAmount = row.bgFareAmount;
            record.tradeType = row.tradeType;
            record.orderPrice = row.orderPrice;
            record.orderPriceTax = row.orderPriceTax;
            record.bgOrderPriceTax = row.bgOrderPriceTax;
            record.orderAmount = row.orderAmount;
            record.orderAmountTax = row.orderAmountTax;
            record.bargainId = row.bargainId;
            record.childList = row.childList;

            if(this.ptype != 'bargain'){
              this.model.attachment = row.attachment;
              this.model.comment = row.comment;
            }
          }

          this.dataSource.push(record);
          this.$forceUpdate()
        })
      },

		}
	}
</script>

<style lang="less" scoped>
	.ant-row.ant-form-item {
		margin-bottom: 12px;
	}
</style>
<style>
rfq-content {
  min-height: 50px;
  display: flex;
}
.rfq-content table {
  width: 100%;
  margin-left: 5px;
  margin-right: 5px;
  border: 1px #ccc solid;
}
.rfq-content table thead th {
  text-align: center;
  color: rgba(0, 0, 0, 0.85);
  font-weight: 500;
  background: #fafafa;
  border: 1px solid #e8e8e8;
  transition: background 0.3s ease;
}
.rfq-content table tbody tr td {
  font-size: 12px;
  border-right: 1px #ccc solid;
  padding-left: 5px;
  padding-right: 5px;
  padding-top: 5px;
  padding-bottom: 5px;
  border-bottom: 1px #ccc solid;
}
</style>
