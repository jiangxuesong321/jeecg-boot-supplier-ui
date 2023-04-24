<template>
	<a-spin :spinning="confirmLoading">
		<j-form-container :disabled="formDisabled">
			<!-- 主表单区域 -->
			<a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
				<a-row>

					<a-col :span="6">
						<a-form-model-item label="报价币种" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="currency">
							<j-dict-select-tag placeholder="请选择报价币种" v-model="model.currency"
								dictCode="currency_type" />
						</a-form-model-item>
					</a-col>
					<a-col :span="6">
						<a-form-model-item label="是否含税" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="biddingIsIncludeTax">
							<a-input v-model="model.biddingIsIncludeTax" placeholder="请输入是否含税报价"></a-input>
						</a-form-model-item>
					</a-col>
					<a-col :span="6">
						<a-form-model-item label="报价金额" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="finalAmount">
							<a-input-number v-model="model.finalAmount" placeholder="请输入报价金额" style="width: 100%" />
						</a-form-model-item>
					</a-col>
					<a-col :span="6">
						<a-form-model-item label="当前名次" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="finalAmount">
							<a-input v-model="model.quotationLevel" disabled></a-input>
						</a-form-model-item>
					</a-col>
				</a-row>
				<!-- 子表单区域 -->
<!-- 				<a-tabs v-model="activeKey" @change="handleChangeTabs">
					<a-tab-pane tab="报价明细" :key="refKeys[0]" :forceRender="true">
						<j-vxe-table keep-source :ref="refKeys[0]" :loading="biddingRecordTable.loading"
							:columns="biddingRecordTable.columns" :dataSource="biddingRecordTable.dataSource"
							:maxHeight="300" :disabled="formDisabled" :rowNumber="true" :rowSelection="true"
							:toolbar="true" :alwaysEdit="true" bordered />
					</a-tab-pane>
				</a-tabs> -->
				<a-divider orientation="left" style="color: #00A0E9">
					报价明细
				</a-divider>
				<a-table
				  ref="table"
				  size="small"
				  rowKey="id"
				  class="j-table-force-nowrap"
				  :scroll="{x:true, y:350}"
				  :columns="columns"
				  :dataSource="dataSource"
				  :pagination="false"
				  :loading="loading"
				  @change="handleTableChange">
				  <template slot="price" slot-scope="text, record">
					  <a-input v-model="record.price"></a-input>
				  </template>
				  <template slot="amount" slot-scope="text, record">
					  <a-input v-model="record.amount"></a-input>
				  </template>
				  <template slot="deliveryTime" slot-scope="text, record">
					  <j-date class="query-group-cust" v-model="record.deliveryTime" style="width:100%"></j-date>
				  </template>
				  <template slot="prodSpecTypeQuotation" slot-scope="text, record">
					  <a-input v-model="record.prodSpecTypeQuotation" suffix="片/月"></a-input>
				  </template>
				  <template slot="comment" slot-scope="text, record">
						<a-input v-model="record.comment"></a-input>
				  </template>
				</a-table>
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
							<a-form-item label="附件" :labelCol="spans.labelCol1" :wrapperCol="spans.wrapperCol1"
								required>
								<j-upload v-model="model.attachment" :trigger-change="true">
								</j-upload>
							</a-form-item>
						</a-col>
					</a-row>
				</a-card>
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
    import { billListMixin } from '../../mixins/billListMixin'
    import { billModalMixin } from '../../mixins/billModalMixin'
	export default {
		name: 'BiddingMainForm',
		mixins: [JVxeTableModelMixin,billListMixin, billModalMixin],
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
				// 新增时子表默认添加几行空数据
				addDefaultRowNum: 1,
				validatorRules: {},
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
						title: '设备标识',
						align: "center",
						dataIndex: 'prodCode',
						width: 100,
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
						},
						width: 200,
					},
					{
						title: '产能要求',
						align: "center",
						dataIndex: 'prodSpecType',
						customRender: (value, row, index) => { //表体的数据列样式
							const obj = {
						  children: value,
								attrs: {},
							};
							obj.attrs.align = 'left';
							return obj;
						},
						width: 100,
					},
					{
						title: '单位',
						align: "center",
						dataIndex: 'unitName',
						width: 80,
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
						},
						width: 100,
					},
					{
						title: '预定交期',
						align: "center",
						dataIndex: 'recordDeliveryTime',
						customRender: (value, row, index) => { //表体的数据列样式
							const obj = {
						  children: value,
								attrs: {},
							};
							obj.attrs.align = 'right';
							return obj;
						},
						width: 120,
					},
					{
						title: '单价',
						align: "center",
						dataIndex: 'price',
						scopedSlots: {
							customRender: 'price'
						},
						width: 120,
					},
					{
						title: '金额',
						align: "center",
						dataIndex: 'amount',
						scopedSlots: {
							customRender: 'amount'
						},
						width: 120,
					},
					{
						title: '交期',
						align: "center",
						dataIndex: 'deliveryTime',
						scopedSlots: {
							customRender: 'deliveryTime'
						},
						width: 140,
					},
					{
						title: '产能',
						align: "center",
						dataIndex: 'prodSpecTypeQuotation',
						scopedSlots: {
							customRender: 'prodSpecTypeQuotation'
						},
						width: 120,
					},
					{
						title: '备注',
						align: "center",
						dataIndex: 'comment',
						scopedSlots: {
							customRender: 'comment'
						},
						width: 200,
					},
				],
				dataSource: [
					{prodCode:'131312',prodName:'测试标的1',prodSpecType:'100片/月',unitName:'台',quantity:'100',recordDeliveryTime:'2022-07-31', price:'', amount:'',deliveryTime:'',prodSpecTypeQuotation:'',comment:''},
					{prodCode:'231315',prodName:'测试标的1',prodSpecType:'200片/月',unitName:'台',quantity:'50',recordDeliveryTime:'2022-07-31', price:'', amount:'',deliveryTime:'',prodSpecTypeQuotation:'',comment:''},
					{prodCode:'231316',prodName:'测试标的1',prodSpecType:'150片/月',unitName:'台',quantity:'80',recordDeliveryTime:'2022-07-31', price:'', amount:'',deliveryTime:'',prodSpecTypeQuotation:'',comment:''},
				],
				refKeys: ['biddingRecord', 'biddingSupplier', 'biddingProfessionals', ],
				tableKeys: ['biddingRecord', 'biddingSupplier', 'biddingProfessionals', ],
				activeKey: 'biddingRecord',
				// 招标明细表
				biddingRecordTable: {
					loading: false,
					dataSource: [],
					columns: [{
							title: '招标记录ID',
							key: 'id',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
							validateRules: [{
								required: true,
								message: '${title}不能为空'
							}],
						},
						{
							title: '招标ID',
							key: 'biddingId',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '招标物品ID',
							key: 'productId',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '需求记录ID',
							key: 'toRecordId',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '数量',
							key: 'recordNumber',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '单位',
							key: 'recordUnit',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
							validateRules: [{
								required: true,
								message: '${title}不能为空'
							}],
						},
						{
							title: '交期',
							key: 'recordDeliveryTime',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '交期单位',
							key: 'recordDeliveryUnit',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},



						{
							title: '询价状态',
							key: 'status',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '中标供应商ID',
							key: 'suppId',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '驳回原因',
							key: 'backReason',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '询价物品名称',
							key: 'productName',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '询价物品规格描述',
							key: 'productSpec',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '备注',
							key: 'comment',
							type: JVXETypes.textarea,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
					]
				},
				// 招标邀请供应商
				biddingSupplierTable: {
					loading: false,
					dataSource: [],
					columns: [{
							title: '招标ID',
							key: 'biddingId',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '招标记录ID',
							key: 'recordId',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
							validateRules: [{
								required: true,
								message: '${title}不能为空'
							}],
						},
						{
							title: '是否推荐',
							key: 'isRecommend',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
							validateRules: [{
								required: true,
								message: '${title}不能为空'
							}],
						},
						{
							title: '状态(0:已受理、1:放弃、2未受理、3过期,4:淘汰)',
							key: 'status',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
							validateRules: [{
								required: true,
								message: '${title}不能为空'
							}],
						},
						{
							title: '驳回供应商不能再次选择(0:正常、2:重新询价)',
							key: 'delFlag',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
							validateRules: [{
								required: true,
								message: '${title}不能为空'
							}],
						},
						{
							title: '备注',
							key: 'comment',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
					]
				},
				// bidding_professionals
				biddingProfessionalsTable: {
					loading: false,
					dataSource: [],
					columns: [{
							title: '招标ID',
							key: 'biddingId',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
							validateRules: [{
								required: true,
								message: '${title}不能为空'
							}],
						},
						{
							title: '请购专家ID',
							key: 'professionalId',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
							validateRules: [{
								required: true,
								message: '${title}不能为空'
							}],
						},
						{
							title: '评标类型 0：技术 1：商务',
							key: 'biddingEvaluateType',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},

						{
							title: '备注',
							key: 'comment',
							type: JVXETypes.textarea,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},

					]
				},
				url: {
					add: "/srm/biddingMain/add",
					edit: "/srm/biddingMain/edit",
					queryById: "/srm/biddingMain/queryById",
					biddingRecord: {
						list: '/srm/biddingMain/queryBiddingRecordByMainId'
					},
					biddingSupplier: {
						list: '/srm/biddingMain/queryBiddingSupplierByMainId'
					},
					biddingProfessionals: {
						list: '/srm/biddingMain/queryBiddingProfessionalsByMainId'
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
				this.biddingRecordTable.dataSource = []
				this.biddingSupplierTable.dataSource = []
				this.biddingProfessionalsTable.dataSource = []
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
					this.requestSubTableData(this.url.biddingRecord.list, params, this.biddingRecordTable)
					this.requestSubTableData(this.url.biddingSupplier.list, params, this.biddingSupplierTable)
					this.requestSubTableData(this.url.biddingProfessionals.list, params, this.biddingProfessionalsTable)
				}
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
					biddingRecordList: allValues.tablesValue[0].tableData,
					biddingSupplierList: allValues.tablesValue[1].tableData,
					biddingProfessionalsList: allValues.tablesValue[2].tableData,
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
