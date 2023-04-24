<template>
	<a-spin :spinning="confirmLoading">
		<j-form-container :disabled="formDisabled">
			<a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
				<a-row>
					<a-col :span="8">
						<a-form-model-item label="合同名称" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="contractId">
              <j-select-contract ref='contract' v-model="model.contractId" :multi="false" @change="backUser"></j-select-contract>
						</a-form-model-item>
					</a-col>
          <a-col :span="8">
            <a-form-model-item label="合同编码" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractNumber">
              <a-input v-model="model.contractNumber" placeholder="请输入合同编码" disabled></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="币种" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="currency">
              <j-dict-select-tag placeholder="请选择币种" v-model="model.currency" dictCode="currency_type" disabled/>
            </a-form-model-item>
          </a-col>
					<a-col :span="8">
						<a-form-model-item label="发票类型" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="invoiceType">
<!--							<j-dict-select-tag placeholder="请选择发票类型" v-model="model.invoiceType" dictCode="invoice_type"/>-->
              <a-select placeholder="请选择发票类型" v-model="model.invoiceType" :disabled='!model.contractId' @change='setVal()'>
                <a-select-option  value="0" v-if='model.currency == "RMB"'>增值税专用发票</a-select-option>
                <a-select-option  value="1" v-if='model.currency == "RMB"'>增值税普通发票</a-select-option>
                <a-select-option  value="2" v-if='model.currency != "RMB"'>形式发票</a-select-option>
              </a-select>
						</a-form-model-item>
					</a-col>
					<a-col :span="8">
						<a-form-model-item label="开票日期" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="invoiceDate">
							<j-date placeholder="请选择开票日期" v-model="model.invoiceDate" style="width: 100%" />
						</a-form-model-item>
					</a-col>
					<a-col :span="8">
						<a-form-model-item label="发票号码" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="invoiceNo">
							<a-input v-model="model.invoiceNo" placeholder="请输入发票号码"></a-input>
						</a-form-model-item>
					</a-col>
					<a-col :span="8">
						<a-form-model-item label="开票金额原币(未税)" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="invoiceAmount">
							<a-input-number v-model="model.invoiceAmount" placeholder="请输入开票金额(未税)"  style="width: 100%" @change='changeAmount' :disabled='dataSource == null || dataSource.length == 0'/>
						</a-form-model-item>
					</a-col>
					<a-col :span="8" v-if='model.invoiceType != "2"'>
						<a-form-model-item label="开票金额原币(含税)" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="invoiceAmountTax">
							<a-input-number v-model="model.invoiceAmountTax" placeholder="请输入开票金额(含税)"  style="width: 100%" @change='changeAmountTax' :disabled='dataSource == null || dataSource.length == 0'/>
						</a-form-model-item>
					</a-col>
          <a-col :span="8" v-if='model.invoiceType != "2"'>
            <a-form-model-item label="税额" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="invoiceTax">
              <a-input-number v-model="model.invoiceTax" placeholder="请输入税额" style="width: 100%" @change='changeTax' :disabled='dataSource == null || dataSource.length == 0'/>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" v-if='model.invoiceType == "2"'>
            <a-form-model-item label="开票金额本币(未税)" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
                               prop="invoiceAmountLocal">
              <a-input-number v-model="model.invoiceAmountLocal" placeholder="请输入开票金额(未税)" disabled style="width: 100%" />
            </a-form-model-item>
          </a-col>
<!--          <a-col :span="8" v-if='model.invoiceType == "2"'>-->
<!--            <a-form-model-item label="开票金额本币(含税)" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="invoiceAmountTaxLocal">-->
<!--              <a-input-number v-model="model.invoiceAmountTaxLocal" placeholder="请输入开票金额(含税)" disabled style="width: 100%" />-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
					<a-divider orientation="left" style="color: #00A0E9">
						发票明细
					</a-divider>
          <a-button type='primary' @click='openDrawer' icon='plus' style='float: right;z-index: 999'>选择合同设备</a-button>
					<a-table
					  ref="table"
					  size="small"
					  rowKey="id"
					  bordered
					  :scroll="{x:1200}"
					  :columns="columns"
					  :dataSource="dataSource"
					  :pagination="false"
					  :loading="loading">
            <template slot='qty' slot-scope='text,record'>
              <a-input-number v-model='record.qty'></a-input-number>
            </template>
            <template slot='invoiceRate' slot-scope='text,record'>
              <a-input-number v-model='record.invoiceRate' @change='setAmount'></a-input-number>
            </template>
					  <span slot="action" slot-scope="text, record,index">
              <a @click='handleDelete(index)'>删除</a>
					  </span>
					</a-table>
					<a-divider orientation="left" style="color: #00A0E9">开票附件</a-divider>
					<a-col :span="24">
						<a-form-model-item label="" :labelCol="spans.labelCol1" :wrapperCol="spans.wrapperCol1">
              <j-upload isMultiple  v-model="model.attachment" ></j-upload>
						</a-form-model-item>
					</a-col>
				</a-row>
			</a-form-model>
      <contract-object-modal ref='modal' :contractId='model.contractId' @ok='back' ptype='invoice'></contract-object-modal>
		</j-form-container>
	</a-spin>
</template>

<script>
import '@/assets/less/TableExpand.less'
import JEllipsis from '@/components/jeecg/JEllipsis'
import { getAction, httpAction } from '@/api/manage'
import { billListMixin } from '../../mixins/billListMixin'
import { billModalMixin } from '../../mixins/billModalMixin'
import { JeecgListMixin } from '@/mixins/JeecgListMixin'
import ContractObjectModal from '@views/pay/modules/ContractObjectModal'
import JSelectContract from '@comp/jeecgbiz/JSelectContract'
import { iegAmount, isNotNullOrEmpty, isNullOrEmpty, preciseNum } from '@/utils/util'

export default {
		name: 'PurchasePayInoviceForm',
		mixins: [JeecgListMixin, billListMixin, billModalMixin],
		components: {JEllipsis,ContractObjectModal,JSelectContract},
		props: {
			//表单禁用
			disabled: {
				type: Boolean,
				default: false,
				required: false
			}
		},
		data() {
			return {
				disableMixinCreated: true,
				model: {
          invoiceType:undefined
        },
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
				confirmLoading: false,
				validatorRules: {
          invoiceDate:[
            {
              required: true,
              message: '请选择开票日期!'
            },
          ],
          invoiceType:[
            {
              required: true,
              message: '请选择发票类型!'
            },
          ],
          invoiceNo:[
            {
              required: true,
              message: '请输入发票号码!'
            },
          ],
					taxRate: [{
						required: true,
						message: '请输入发票税率!'
					}, ],
					invoiceAmount: [{
						required: true,
						message: '请输入开票金额(未税)!'
					}, ],
					invoiceAmountTax: [{
						required: true,
						message: '请输入开票金额(含税)!'
					}, ],
				},
				url: {
					list: "/srm/contractBase/list",
					add: "/srm/purchasePayInovice/add",
					edit: "/srm/purchasePayInovice/edit",
					queryById: "/srm/purchasePayInovice/queryById"
				},
				columns: [{
						title: '序号',
						dataIndex: '',
						key: 'rowIndex',
						width: 60,
						align: "center",
						customRender: function(t, r, index) {
							return parseInt(index) + 1;
						}
					},
					{
						title: '设备编号',
						align: "center",
						dataIndex: 'prodCode',
            width: 120,
					},
					{
						title: '设备名称',
						align: "center",
						dataIndex: 'prodName',
            width: 120,
					},
					{
						title: '规格型号',
						align: "center",
						dataIndex: 'prodSpecType',
            ellipsis:true,
            width: 120,
					},
					{
						title: '单位',
						align: "center",
						dataIndex: 'unitId_dictText',
            width: 120,
					},
					{
						title: '数量',
						align: "center",
						dataIndex: 'qty',
            width: 120,
            // scopedSlots: {customRender: 'qty'}
					},
					{
						title: '单价',
						align: "center",
						dataIndex: 'contractPrice',
            width: 120,
            customRender: (t,r,index) => {
              let _this = this;
              let icon = "";
              if(_this.model.currency == 'RMB'){
                icon = '¥';
              }else if(_this.model.currency == 'JPY'){
                icon = 'Ұ';
              }else if(_this.model.currency == 'USD'){
                icon = '$';
              }else if(_this.model.currency == 'EUR'){
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
						title: '金额(未税)',
						align: "center",
						dataIndex: 'contractAmount',
            width: 120,
            customRender: (t,r,index) => {
              let _this = this;
              let icon = "";
              if(_this.model.currency == 'RMB'){
                icon = '¥';
              }else if(_this.model.currency == 'JPY'){
                icon = 'Ұ';
              }else if(_this.model.currency == 'USD'){
                icon = '$';
              }else if(_this.model.currency == 'EUR'){
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
						title: '税率(%)',
						align: "center",
						dataIndex: 'contractTaxRate',
            width: 120,
					},
					{
						title: '税额',
						align: "center",
						dataIndex: 'invoiceTax',
            width: 120,
            customRender: (t,r,index) => {
              let _this = this;
              let icon = "";
              if(_this.model.currency == 'RMB'){
                icon = '¥';
              }else if(_this.model.currency == 'JPY'){
                icon = 'Ұ';
              }else if(_this.model.currency == 'USD'){
                icon = '$';
              }else if(_this.model.currency == 'EUR'){
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
            title: '开票比例(%)',
            align: "center",
            dataIndex: 'invoiceRate',
            width: 120,
            // scopedSlots: {customRender: 'invoiceRate'}
          },
					{
						title: '操作',
						dataIndex: 'action',
						align: "center",
						width: 147,
						scopedSlots: {customRender: 'action'}
					}
				],
			}
		},
		computed: {
			formDisabled() {
				return this.disabled
			},
		},
		created() {
			//备份model原始值
			this.modelDefault = JSON.parse(JSON.stringify(this.model));
		},
		methods: {
      changeTax(){
        let invoiceTax = this.model.invoiceTax;
        if(this.dataSource == null || this.dataSource.length == 0){
          this.$message.error("请选择需要开票的明细");
          return;
        }
        let invoiceAmount = this.model.invoiceAmount;
        let invoiceAmountTax = this.model.invoiceAmountTax;

        if(isNotNullOrEmpty(invoiceAmount)){
          this.model.invoiceAmountTax = Number(invoiceAmount) + Number(invoiceTax);
        }else if(isNotNullOrEmpty(invoiceAmountTax)){
          this.model.invoiceAmount = Number(invoiceAmountTax) - Number(invoiceTax);
        }
        this.$forceUpdate();
      },
      changeAmountTax(){
        let invoiceAmountTax = this.model.invoiceAmountTax;
        if(this.dataSource == null || this.dataSource.length == 0){
          this.$message.error("请选择需要开票的明细");
          return;
        }
        if(isNotNullOrEmpty(invoiceAmountTax) && invoiceAmountTax > 0){
          let contractAmountTax = 0;
          this.dataSource.filter(item => {
            contractAmountTax = Number(contractAmountTax) + Number(item.contractAmountTax);
          })
          this.dataSource.filter(item => {
            let invoiceRate = item.invoiceRate;
            invoiceRate = Number(preciseNum(Number(invoiceAmountTax) / Number(contractAmountTax) * 100,0));
            item.invoiceRate = invoiceRate;
            this.$forceUpdate();
          })
        }

        let invoiceAmount = this.model.invoiceAmount;
        let invoiceTax = this.model.invoiceTax;
        if(isNotNullOrEmpty(invoiceAmount)){
          this.model.invoiceTax = Number(preciseNum(Number(invoiceAmountTax) - Number(invoiceAmount),2));
        }else if(isNotNullOrEmpty(invoiceTax)){
          this.model.invoiceAmount = Number(preciseNum(Number(invoiceAmountTax) - Number(invoiceTax),2));
        }

        if(this.model.currency == 'RMB'){
          this.model.invoiceAmountTaxLocal = invoiceAmountTax;
        }
        this.$forceUpdate();
      },
      changeAmount(){
        let invoiceAmount = this.model.invoiceAmount;
        if(this.dataSource == null || this.dataSource.length == 0){
          this.$message.error("请选择需要开票的明细");
          return;
        }
        if(isNotNullOrEmpty(invoiceAmount) && invoiceAmount > 0){
          let contractAmount = 0;
          this.dataSource.filter(item => {
            contractAmount = Number(contractAmount) + Number(item.contractAmount);
          })
          this.dataSource.filter(item => {
            let invoiceRate = item.invoiceRate;
            invoiceRate = Number(preciseNum(Number(invoiceAmount) / Number(contractAmount) * 100,0));
            item.invoiceRate = invoiceRate;
            this.$forceUpdate();
          })
        }

        let invoiceAmountTax = this.model.invoiceAmountTax;
        let invoiceTax = this.model.invoiceTax;
        if(isNotNullOrEmpty(invoiceAmountTax)){
          this.model.invoiceTax = Number(preciseNum(Number(invoiceAmountTax) - Number(invoiceAmount),2));
        }else if(isNotNullOrEmpty(invoiceTax)){
          this.model.invoiceAmountTax = Number(preciseNum(Number(invoiceAmount) + Number(invoiceTax),2));
        }
        if(this.model.currency != 'RMB'){
          this.model.invoiceTax = 0;
          this.model.invoiceAmountTax = invoiceAmount;

          let contractExchangeRate = this.model.contractExchangeRate;
          this.model.invoiceAmountLocal = preciseNum(Number(invoiceAmount) / Number(contractExchangeRate),2);
          this.model.invoiceAmountTaxLocal = preciseNum(Number(invoiceAmount) / Number(contractExchangeRate),2);
        }else{
          this.model.invoiceAmountLoca = invoiceAmount;
        }
        this.$forceUpdate();
      },
      setVal(){
        this.$forceUpdate();
      },
      handleDelete(index){
        this.dataSource.splice(index,1);
        this.setAmount();
      },
      back(rows){
        rows.filter(item => {
          let flag = false;
          this.dataSource.filter(child => {
            if(item.id == child.id){
              flag = true;
              return;
            }
          })
          if(!flag){
            item.invoiceRate = 100 - Number(item.invoiceRate);
            item.billDetailId = item.id;
            item.taxRate = this.model.taxRate;
            let invoiceTax = Number(item.contractAmountTax) - Number(item.contractAmount);
            item.invoiceTax = Number(preciseNum(invoiceTax,2));
            this.dataSource.push(item);
          }
        })
        this.$message.success("添加成功");
        // setTimeout(() => {
        //   this.setAmount();
        // }, 500)
      },
      setAmount(){
        let invoiceAmount = 0;
        let invoiceAmountTax = 0;
        let invoiceAmountLocal = 0;
        let invoiceAmountTaxLocal = 0;
        this.dataSource.filter(item => {
          invoiceAmount = (Number(invoiceAmount) + Number(item.contractAmount)) * (Number(item.invoiceRate) / 100);
          invoiceAmountTax = (Number(invoiceAmountTax) + Number(item.contractAmountTax)) * (Number(item.invoiceRate) / 100);

          invoiceAmountLocal = (Number(invoiceAmountLocal) + Number(item.contractAmountLocal)) * (Number(item.invoiceRate) / 100);
          invoiceAmountTaxLocal = (Number(invoiceAmountTaxLocal) + Number(item.contractAmountTaxLocal)) * (Number(item.invoiceRate) / 100);
        })
        this.model.invoiceAmount = Number(preciseNum(invoiceAmount,2));
        this.model.invoiceAmountTax = Number(preciseNum(invoiceAmountTax,2));
        this.model.invoiceAmountLocal = Number(preciseNum(invoiceAmountLocal,2));
        this.model.invoiceAmountTaxLocal = Number(preciseNum(invoiceAmountTaxLocal,2));
        let invoiceTax = Number(invoiceAmountTax) - Number(invoiceAmount);
        this.model.invoiceTax = Number(preciseNum(invoiceTax,2));
        this.$forceUpdate();
      },
      openDrawer(){
        let contractId = this.model.contractId;
        if(isNullOrEmpty(contractId)){
          this.$message.error("请选择合同");
          return;
        }
        this.$refs.modal.loadData();
      },
      backUser(ids,rows){
        this.model.contractName = rows[0].contractName;
        this.model.contractNumber = rows[0].contractNumber;
        this.model.taxRate = rows[0].contractTaxRate;
        this.model.currency = rows[0].contractCurrency;
        this.model.contractExchangeRate = rows[0].contractExchangeRate;
        if(this.model.currency != 'RMB'){
          this.model.invoiceType = '2';
        }else{
          this.model.invoiceType = '0';
        }
        this.getProjectById(rows[0].projectId);
        this.dataSource = [];
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
			add() {
				this.edit(this.modelDefault);
			},
			edit(record) {
				this.model = Object.assign({}, record);
				this.visible = true;
				if(this.model.id){
          this.fetchDetailList(this.model.id);
        }
			},
      fetchDetailList(invoiceId){
        let url = "/srm/purchasePayInovice/queryPurPayInvoiceDetailByMainId";
        let param = {
          id:invoiceId
        }
        getAction(url,param).then(res => {
          this.dataSource = res.result;
        })
      },
      submitDraft(){
        const that = this;
        // 触发表单验证
        that.$refs.form.validate(valid => {
          if (true) {

            let httpurl = '/srm/purchasePayInovice/draft';
            let method = 'post';

            that.model.detailList = that.dataSource;
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
			submitForm() {
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
						let dataSource = that.dataSource;
						if(dataSource == null || dataSource.length == 0){
              that.$message.error("请选择需要开票的合同设备");
              return
            }
						for(let i = 0; i < dataSource.length; i++){
						  if(isNullOrEmpty(dataSource[i].invoiceRate)){
                that.$message.error("第"+(i+1)+"行,请输入开票比例");
                return
              }
            }
            that.model.detailList = dataSource;
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
		}
	}
</script>
<style lang="less" scoped>
	.ant-row.ant-form-item {
		margin-bottom: 12px;
	}
</style>
