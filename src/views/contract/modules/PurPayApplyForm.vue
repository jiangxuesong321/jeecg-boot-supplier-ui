<template>
	<a-spin :spinning="confirmLoading">
		<j-form-container :disabled="formDisabled">
			<!-- 主表单区域 -->
			<a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
				<a-row>
					<a-divider orientation="left" style="color: #00A0E9">
						基本信息
					</a-divider>
					<a-col :span="8">
						<a-form-model-item label="收款申请单号" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="applyCode">
							<a-input v-model="model.applyCode" placeholder="请输入收款申请单号" disabled></a-input>
						</a-form-model-item>
					</a-col>
					<a-col :span="8">
						<a-form-model-item label="合同编号" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="contractName">
							<a-input v-model="model.contractNumber" placeholder="请输入合同名称" disabled></a-input>
						</a-form-model-item>
					</a-col>
					<a-col :span="8">
						<a-form-model-item label="合同名称" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="contractName">
							<a-input v-model="model.contractName" disabled></a-input>
						</a-form-model-item>
					</a-col>
					<a-col :span="8">
						<a-form-model-item label="合同类型" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="contractName">
							<a-input v-model="model.contractType" disabled></a-input>
						</a-form-model-item>
					</a-col>
					<a-col :span="8">
						<a-form-model-item label="合同总金额(原币)" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="contractName">
							<a-input v-model="model.contractAmountTax" disabled></a-input>
						</a-form-model-item>
					</a-col>
					<a-col :span="8">
						<a-form-model-item label="合同生效日期" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="contractName">
							<a-input v-model="model.contractValidDate" disabled></a-input>
						</a-form-model-item>
					</a-col>
				</a-row>
				<a-row>
					<a-divider orientation="left" style="color: #00A0E9">
						收款信息
					</a-divider>
					<a-col :span="8">
						<a-form-model-item label="币种" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="contractCurrency">
							<j-dict-select-tag v-model="model.contractCurrency" placeholder="请选择合同币种"
								dictCode="currency_type" disabled />
						</a-form-model-item>
					</a-col>
					<a-col :span="8">
						<a-form-model-item label="收款类型" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="payMethod">
							<j-dict-select-tag v-model="model.contractCurrency" placeholder="请选择合同币种"
								dictCode="'contract_pay_step, pay_step, id, contract_id=\''+model.contractId+'\''"
								disabled />
						</a-form-model-item>
					</a-col>
					<a-col :span="8">
						<a-form-model-item label="收款方式" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="payMethod">
							<j-dict-select-tag v-model="model.payMethod" placeholder="请选择合同币种" dictCode="currency_type"
								disabled />
						</a-form-model-item>
					</a-col>
					<a-col :span="8">
						<a-form-model-item label="申请金额原币" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="payAmountOther">
							<a-input-number v-model="model.payAmountOther" placeholder="请输入申请金额原币"
								style="width: 100%" />
						</a-form-model-item>
					</a-col>
					<!-- 					<a-col :span="12">
						<a-form-model-item label="申请理由" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="applyReason">
							<a-input v-model="model.applyReason" placeholder="请输入申请理由"></a-input>
						</a-form-model-item>
					</a-col> -->
					<!-- 					<a-divider orientation="left" style="color: #00A0E9">
						账号信息
					</a-divider> -->
					<!-- 					<a-col :span="12">
						<a-form-model-item label="供应商名称" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="suppName">
							<a-input v-model="model.suppName" placeholder="请输入供应商名称"></a-input>
						</a-form-model-item>
					</a-col> -->
					<a-col :span="8">
						<a-form-model-item label="收款开户行" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="receivingBank">
							<a-input v-model="model.receivingBank" placeholder="请输入收款开户行"></a-input>
						</a-form-model-item>
					</a-col>
					<a-col :span="8">
						<a-form-model-item label="收款账号" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="receivingNumber">
							<a-input v-model="model.receivingNumber" placeholder="请输入收款账号"></a-input>
						</a-form-model-item>
					</a-col>
					<a-col :span="8">
						<a-form-model-item label="应收款日期" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="dueDate">
							<j-date placeholder="请选择应收款日期" v-model="model.dueDate" style="width: 100%" />
						</a-form-model-item>
					</a-col>

					<a-col :span="24">
						<a-form-model-item label="备注" :labelCol="spans.labelCol1" :wrapperCol="spans.wrapperCol1"
							prop="comment">
							<a-input v-model="model.comment" placeholder="请输入备注"></a-input>
						</a-form-model-item>
					</a-col>


				</a-row>

		<a-divider orientation="left" style="color: #00A0E9">
			收款申请明细
		</a-divider>
<!-- 		<j-vxe-table keep-source :ref="refKeys[0]" :loading="purPayApplyDetailTable.loading" :alwaysEdit="true"
			:columns="purPayApplyDetailTable.columns" :dataSource="purPayApplyDetailTable.dataSource" :maxHeight="300"
			:disabled="formDisabled" :rowNumber="true" :rowSelection="true" :toolbar="true" /> -->
			<div class="table-operator">
				<a-button type='primary' @click='searchQuery' icon='search'>添加明细</a-button>
			</div>
			<a-table
			  ref="table"
			  size="small"
			  rowKey="id"
			  class="j-table-force-nowrap"
			  :scroll="{x:true}"
			  :columns="columns"
			  :dataSource="dataSource"
			  :pagination="false"
			  :loading="loading"
			  @change="handleTableChange">
			  <span slot="action" slot-scope="text, record">
				<a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
					<a>删除</a>
				</a-popconfirm>
			  </span>
			</a-table>
			<a-row>
				<a-divider orientation="left" style="color: #00A0E9">
					文件
				</a-divider>
				<a-col :span="24">
					<a-form-model-item label="发票" :labelCol="spans.labelCol1" :wrapperCol="spans.wrapperCol1"
						prop="logoUrl">
						<j-upload isMultiple v-model="model.logoUrl"></j-upload>
					</a-form-model-item>
				</a-col>

				<a-col :span="24">
					<a-form-model-item label="补充材料" :labelCol="spans.labelCol1" :wrapperCol="spans.wrapperCol1"
						prop="supplierAttachment">
						<j-upload isMultiple v-model="model.supplierAttachment"></j-upload>
					</a-form-model-item>
				</a-col>
			</a-row>
			</a-form-model>
		</j-form-container>
	</a-spin>
</template>

<script>
	import {
		getAction
	} from '@/api/manage'
	import {
		JVxeTableModelMixin
	} from '@/mixins/JVxeTableModelMixin.js'
	import {
		JVXETypes
	} from '@/components/jeecg/JVxeTable'
	import {
		getRefPromise,
		VALIDATE_FAILED
	} from '@/components/jeecg/JVxeTable/utils/vxeUtils.js'
	import {
		validateDuplicateValue
	} from '@/utils/util'
	import JFormContainer from '@/components/jeecg/JFormContainer'
	import {
		billListMixin
	} from '../../mixins/billListMixin'
	import {
		billModalMixin
	} from '../../mixins/billModalMixin'

	export default {
		name: 'PurPayApplyForm',
		mixins: [JVxeTableModelMixin, billListMixin, billModalMixin],
		components: {
			JFormContainer,
		},
		data() {
			return {
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
				model: {},
				columns: [{
						title: '#',
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
						dataIndex: 'prodCode'
					},
					{
						title: '设备名称',
						align: "center",
						dataIndex: 'prodName',
						customRender: (value, row, index) => { //表体的数据列样式
							const obj = {
						  children: value,
								attrs: {},
							};
							obj.attrs.align = 'left';
							return obj;
						}
					},
					{
						title: '规格型号',
						align: "center",
						dataIndex: 'prodSpecType',
						customRender: (value, row, index) => { //表体的数据列样式
							const obj = {
						  children: value,
								attrs: {},
							};
							obj.attrs.align = 'left';
							return obj;
						}
					},
					{
						title: '单位',
						align: "center",
						dataIndex: 'unitName'
					},
					{
						title: '数量',
						align: "center",
						dataIndex: 'quantity',
						customRender: (value, row, index) => { //表体的数据列样式
							const obj = {
						  children: value,
								attrs: {},
							};
							obj.attrs.align = 'right';
							return obj;
						}
					},
					{
						title: '单价',
						align: "center",
						dataIndex: 'contractPrice',
						customRender: (value, row, index) => { //表体的数据列样式
							const obj = {
						  children: value,
								attrs: {},
							};
							obj.attrs.align = 'right';
							return obj;
						}
					},
					{
						title: '金额',
						align: "center",
						dataIndex: 'contractAmount',
						customRender: (value, row, index) => { //表体的数据列样式
							const obj = {
						  children: value,
								attrs: {},
							};
							obj.attrs.align = 'right';
							return obj;
						}
					},
					{
						title: '操作',
						dataIndex: 'action',
						align: "center",
						fixed: "right",
						width: 147,
						scopedSlots: {
							customRender: 'action'
						}
					}
				],
				// 新增时子表默认添加几行空数据
				addDefaultRowNum: 1,
				validatorRules: {},
				refKeys: ['purPayApplyDetail', ],
				tableKeys: ['purPayApplyDetail', ],
				activeKey: 'purPayApplyDetail',
				// 收款申请明细
				purPayApplyDetailTable: {
					loading: false,
					dataSource: [],
					columns: [{
							title: '设备编号',
							key: 'prodCode',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '设备名称',
							key: 'prodName',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '设备品牌',
							key: 'prodBrand',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '设备型号',
							key: 'prodSpecType',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '数量',
							key: 'contractPriceTax',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '单价(原币)',
							key: 'contractPriceTax',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '小计(原币)',
							key: 'contractAmountTax',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
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
			formDisabled() {
				return this.disabled
			},
		},
		created() {},
		methods: {
			addBefore() {
				this.purPayApplyDetailTable.dataSource = []
			},
			getAllTable() {
				let values = this.tableKeys.map(key => getRefPromise(this, key))
				return Promise.all(values)
			},
			/** 调用完edit()方法之后会自动调用此方法 */
			editAfter() {
				this.$nextTick(() => {})
				// 加载子表数据
				if (this.model.id) {
					let params = {
						id: this.model.id
					}
					this.requestSubTableData(this.url.purPayApplyDetail.list, params, this.purPayApplyDetailTable)
				}
			},
			payApply(record) {
				this.model = Object.assign({}, record)
			},
			//校验所有一对一子表表单
			validateSubForm(allValues) {
				return new Promise((resolve, reject) => {
					Promise.all([]).then(() => {
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
					purPayApplyDetailList: allValues.tablesValue[0].tableData,
				}
			},
			validateError(msg) {
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
