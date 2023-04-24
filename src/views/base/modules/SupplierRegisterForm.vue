<template>
	<a-card :bordered="false">
		<a-spin :spinning="confirmLoading">
			<j-form-container :disabled="formDisabled">
				<!-- 主表单区域 -->
				<a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
					<a-row>
						<a-divider orientation="left" style="color: #00A0E9">
							基本信息
						</a-divider>
						<a-col :span="8">
							<a-form-model-item label="企业属性" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="name">
								<a-radio-group v-model="model.country" @change="onChangeCountry">
								  <a-radio :value="1">
									国内
								  </a-radio>
								  <a-radio :value="2">
									国外
								  </a-radio>
								</a-radio-group>
							</a-form-model-item>
						</a-col>
						<a-col :span="8">
							<a-form-model-item label="企业名称" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="name">
								<a-input v-model="model.name" placeholder="请输入名称"></a-input>
							</a-form-model-item>
						</a-col>
						<a-col :span="8">
							<a-form-model-item label="企业简称" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
								prop="shortName">
								<a-input v-model="model.shortName" placeholder="请输入简称"></a-input>
							</a-form-model-item>
						</a-col>
						<a-col :span="8">
							<a-form-model-item label="企业地址" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
								prop="address">
								<a-input v-model="model.address" placeholder="请输入企业地址"></a-input>
							</a-form-model-item>
						</a-col>
						<a-col :span="8">
							<a-form-model-item label="企业性质" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
								prop="supplierType">
								<j-multi-select-tag type="select" v-model="model.supplierType" dictCode="supplier_type"
									placeholder="请选择企业性质" />
							</a-form-model-item>
						</a-col>
<!-- 						<a-col :span="8">
							<a-form-model-item label="供应商分类" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
								prop="supplierCategory">
								<a-input v-model="model.supplierCategory" placeholder="请输入供应商分类"></a-input>
							</a-form-model-item>
						</a-col>
 -->

<!-- 						<a-col :span="8">
							<a-form-model-item label="代理等级" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="level">
								<j-dict-select-tag type="list" v-model="model.level" dictCode=""
									placeholder="请选择代理等级" />
							</a-form-model-item>
						</a-col> -->

<!-- 						<a-col :span="8">
							<a-form-model-item label="logo" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
								prop="logoUrl">
								<j-image-upload isMultiple v-model="model.logoUrl"></j-image-upload>
							</a-form-model-item>
						</a-col>
						<a-col :span="8">
							<a-form-model-item label="营业执照" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
								prop="supplierAttachment">
								<j-image-upload isMultiple v-model="model.supplierAttachment"></j-image-upload>
							</a-form-model-item>
						</a-col> -->
						</a-row>
						<a-row>
							<a-divider orientation="left" style="color: #00A0E9">
								营业许可
							</a-divider>
							<a-col :span="8">
								<a-form-model-item :label="sociaCodeLabel" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="name">
									<a-input v-model="model.sociaCode" placeholder="请输入统一社会信用代码"></a-input>
								</a-form-model-item>
							</a-col>
							<a-col :span="8">
								<a-form-model-item label="到期日期" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
									prop="shortName">
									<!-- <a-input v-model="model.deadline" placeholder="请输入到期日期"></a-input> -->
									<j-date placeholder="请输入到期日期" class="query-group-cust"
										v-model="model.deadline"></j-date>
								</a-form-model-item>
							</a-col>
							<a-col :span="8">
								<a-form-model-item label="营业执照附件" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
									prop="address">
									<!-- <a-input v-model="model.attachment" placeholder="请输入企业地址"></a-input> -->
								  <a-upload
								    name="file"
								    :multiple="true"
								    action="https://www.mocky.io/v2/5cc8019d300000980a055e76"
								    :headers="headers"
								    @change="handleChange"
								  >
								    <a-button> <a-icon type="upload" /> Click to Upload </a-button>
								  </a-upload>
								</a-form-model-item>
							</a-col>
						</a-row>	
						<a-row>
						<a-divider orientation="left" style="color: #00A0E9">
							详细信息
						</a-divider>
						<a-col :span="8">
							<a-form-model-item label="公司曾用名" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
								prop="corporate">
								<a-input v-model="model.namePrev" placeholder="请输入公司曾用名"></a-input>
							</a-form-model-item>
						</a-col>
						<a-col :span="8">
							<a-form-model-item label="注册资本" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
								prop="corporate">
								<a-input v-model="model.registerCapital" placeholder="请输入注册资本"></a-input>
							</a-form-model-item>
						</a-col>
						<a-col :span="8">
							<a-form-model-item label="纳税规模" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
								prop="corporate">
								<a-input v-model="model.taxScale" placeholder="请输入纳税规模"></a-input>
							</a-form-model-item>
						</a-col>
						<a-col :span="8">
							<a-form-model-item label="员工人数" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
								prop="corporatePhone">
								<a-input v-model="model.employeeCount" placeholder="请输入员工人数"></a-input>
							</a-form-model-item>
						</a-col>
						<a-col :span="8">
							<a-form-model-item label="法人代表" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
								prop="corporate">
								<a-input v-model="model.corporate" placeholder="请输入法人代表"></a-input>
							</a-form-model-item>
						</a-col>
						<a-col :span="8">
							<a-form-model-item label="法人电话" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
								prop="corporatePhone">
								<a-input v-model="model.corporatePhone" placeholder="请输入法人电话"></a-input>
							</a-form-model-item>
						</a-col>
						<a-col :span="8">
							<a-form-model-item label="企业类型" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
								prop="corporatePhone">
								<a-input v-model="model.companyType" placeholder="请输入企业类型"></a-input>
							</a-form-model-item>
						</a-col>
						<a-col :span="8">
							<a-form-model-item label="注册时间" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
								prop="corporatePhone">
								<a-input v-model="model.registerDate" placeholder="请输入注册时间"></a-input>
							</a-form-model-item>
						</a-col>
						<a-col :span="8">
							<a-form-model-item label="企业邮箱" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
								prop="corporatePhone">
								<a-input v-model="model.companyEmail" placeholder="请输入企业邮箱"></a-input>
							</a-form-model-item>
						</a-col>
						<a-col :span="8">
							<a-form-model-item label="企业传真" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
								prop="corporatePhone">
								<a-input v-model="model.companyFax" placeholder="请输入企业邮箱"></a-input>
							</a-form-model-item>
						</a-col>
						<a-col :span="8">
							<a-form-model-item label="企业网站" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
								prop="corporatePhone">
								<a-input v-model="model.companyWebSite" placeholder="请输入企业网站"></a-input>
							</a-form-model-item>
						</a-col>
					</a-row>
				</a-form-model>
			</j-form-container>
			<!-- 子表单区域 -->
			<a-tabs v-model="activeKey" @change="handleChangeTabs">
				<a-tab-pane tab="联系人" :key="refKeys[0]" :forceRender="true">
					<j-vxe-table keep-source :ref="refKeys[0]" :loading="basSupplierContactTable.loading"
						:columns="basSupplierContactTable.columns" :dataSource="basSupplierContactTable.dataSource"
						:maxHeight="300" :disabled="formDisabled" :rowNumber="true" :rowSelection="true"
						:toolbar="true" :alwaysEdit="true"/>
				</a-tab-pane>
				<a-tab-pane tab="资质证书" :key="refKeys[1]" :forceRender="true">
					<j-vxe-table keep-source :ref="refKeys[1]" :loading="basSupplierQualificationTable.loading"
						:columns="basSupplierQualificationTable.columns"
						:dataSource="basSupplierQualificationTable.dataSource" :maxHeight="300" :disabled="formDisabled"
						:rowNumber="true" :rowSelection="true" :toolbar="true"  :alwaysEdit="true"/>
				</a-tab-pane>
				<a-tab-pane tab="银行账号" :key="refKeys[2]" :forceRender="true">
					<j-vxe-table keep-source :ref="refKeys[2]" :loading="basSupplieBankTable.loading"
						:columns="basSupplieBankTable.columns"
						:dataSource="basSupplieBankTable.dataSource" :maxHeight="300" :disabled="formDisabled"
						:rowNumber="true" :rowSelection="true" :toolbar="true"  :alwaysEdit="true"/>
				</a-tab-pane>
			</a-tabs>
		</a-spin>
		<div class="drawer-footer" style="text-align: center;margin-top: 15px;">
			<a-button @click="handleOk" type="primary" style="margin-bottom: 0;margin-left:5px;">注册</a-button>
		</div>
	</a-card>
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
	import store from '@/store/'

	export default {
		name: 'BasSupplierForm',
		mixins: [JVxeTableModelMixin,billListMixin,billModalMixin],
		components: {
			JFormContainer,
		},
		data() {
			return {
				sociaCodeLabel: '统一社会信用代码',
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
				refKeys: ['basSupplierContact', 'basSupplierQualification', 'basSupplierBank'],
				tableKeys: ['basSupplierContact', 'basSupplierQualification', 'basSupplierBank'],
				activeKey: 'basSupplierContact',
				// 供应商联系人
				basSupplierContactTable: {
					loading: false,
					dataSource: [],
					columns: [{
							title: '序号',
							key: 'orderNum',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						/*          {
						            title: '供应商ID',
						            key: 'supplierId',
						             type: JVXETypes.input,
						            width:"200px",
						            placeholder: '请输入${title}',
						            defaultValue:'',
						          },*/
						{
							title: '联系人',
							key: 'contacter',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '联系人邮箱',
							key: 'contacterEmail',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '联系人电话',
							key: 'contacterTel',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						/*           {
						             title: '联系人传真',
						             key: 'contacterFax',
						              type: JVXETypes.input,
						             width:"200px",
						             placeholder: '请输入${title}',
						             defaultValue:'',
						           },*/
						{
							title: '联系人职位',
							key: 'contacterPosition',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '联系人部门',
							key: 'contacterDepartment',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '联系人地址',
							key: 'contacterAddress',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '是否默认',
							key: 'isDefault',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '状态',
							key: 'status',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '备注',
							key: 'remark',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						/*            {
						              title: '是否开通过SRM账号',
						              key: 'isSrmAccount',
						               type: JVXETypes.input,
						              width:"200px",
						              placeholder: '请输入${title}',
						              defaultValue:'',
						            },
						            {
						              title: '联系人明片',
						              key: 'cardUrl',
						              type: JVXETypes.image,
						              token:true,
						              responseName:"message",
						              width:"200px",
						              placeholder: '请输入${title}',
						              defaultValue:'',
						            }, */
					]
				},
				// 供应商资质证书
				basSupplierQualificationTable: {
					loading: false,
					dataSource: [],
					columns: [
						// {
						//   title: '供应商ID',
						//   key: 'supplierId',
						//    type: JVXETypes.input,
						//   width:"200px",
						//   placeholder: '请输入${title}',
						//   defaultValue:'',
						// },
						{
							title: '资质证书名称',
							key: 'qualificationName',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '有效开始日期',
							key: 'valideDateBegin',
							type: JVXETypes.date,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '有效结束日期',
							key: 'valideDateEnd',
							type: JVXETypes.date,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '状态',
							key: 'status',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '备注',
							key: 'remark',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						/*            {
						              title: '证书路径',
						              key: 'qualUrl',
						              type: JVXETypes.file,
						              token:true,
						              responseName:"message",
						              width:"200px",
						              placeholder: '请选择文件',
						              defaultValue:'',
						            }, */
					]
				},
				// 供应商银行账号
				basSupplieBankTable: {
					loading: false,
					dataSource: [],
					columns: [
						// {
						//   title: '供应商ID',
						//   key: 'supplierId',
						//    type: JVXETypes.input,
						//   width:"200px",
						//   placeholder: '请输入${title}',
						//   defaultValue:'',
						// },
						{
							title: '账号名称',
							key: 'qualificationName',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '开户行',
							key: 'valideDateBegin',
							type: JVXETypes.date,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '银行账号',
							key: 'valideDateEnd',
							type: JVXETypes.date,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '币种',
							key: 'valideDateEnd',
							type: JVXETypes.date,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: 'SWIFT CODE',
							key: 'valideDateEnd',
							type: JVXETypes.date,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '状态',
							key: 'status',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						{
							title: '备注',
							key: 'remark',
							type: JVXETypes.input,
							width: "200px",
							placeholder: '请输入${title}',
							defaultValue: '',
						},
						/*            {
						              title: '证书路径',
						              key: 'qualUrl',
						              type: JVXETypes.file,
						              token:true,
						              responseName:"message",
						              width:"200px",
						              placeholder: '请选择文件',
						              defaultValue:'',
						            }, */
					]
				},
				url: {
					add: "/srm/basSupplier/add",
					edit: "/srm/basSupplier/edit",
					queryById: "/srm/basSupplier/queryById",
					basSupplierContact: {
						list: '/srm/basSupplier/queryBasSupplierContactByMainId'
					},
					basSupplierQualification: {
						list: '/srm/basSupplier/queryBasSupplierQualificationByMainId'
					},
				},
				headers: {
					authorization: 'authorization-text',
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
		mounted() {
			//this.getCompanyInfo()
		},
		methods: {
			getCompanyInfo() {
				let params = {
					id: store.getters.userInfo.id
				}
				getAction(this.url.queryById, params).then((res) => {
					if (res.success) {
						this.model = res.result;
					}
				})
			},
			onChangeCountry(e) {
				if(e.target.value==1) {
					this.sociaCodeLabel = "统一社会信用代码"
				} else {
					this.sociaCodeLabel = "注册登记代码"
				}
			},
			handleChange(info) {
			  if (info.file.status !== 'uploading') {
				console.log(info.file, info.fileList);
			  }
			  if (info.file.status === 'done') {
				this.$message.success(`${info.file.name} file uploaded successfully`);
			  } else if (info.file.status === 'error') {
				this.$message.error(`${info.file.name} file upload failed.`);
			  }
			},
			addBefore() {
				this.basSupplierContactTable.dataSource = []
				this.basSupplierQualificationTable.dataSource = []
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
					this.requestSubTableData(this.url.basSupplierContact.list, params, this.basSupplierContactTable)
					this.requestSubTableData(this.url.basSupplierQualification.list, params, this
						.basSupplierQualificationTable)
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
					basSupplierContactList: allValues.tablesValue[0].tableData,
					basSupplierQualificationList: allValues.tablesValue[1].tableData,
				}
			},
			validateError(msg) {
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