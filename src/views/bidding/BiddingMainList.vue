<template>
	<a-card :bordered="false">
		<!-- 查询区域 -->
		<div class="table-page-search-wrapper">
			<a-form layout="inline" @keyup.enter.native="searchQuery">
				<a-row :gutter="24">
					<a-col :span="6">
						<a-form-item label="招标名称" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
							<a-input placeholder="请输入招标名称" v-model="queryParam.biddingName"></a-input>
						</a-form-item>
					</a-col>
					<a-col :span="6">
						<a-form-item label="招标编码" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
							<a-input placeholder="请输入招标编码" v-model="queryParam.biddingNo"></a-input>
						</a-form-item>
					</a-col>
					<a-col :span="6">
						<a-form-item label="招标日期" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
							<j-date placeholder="请选择开始" class="query-group-cust" v-model="queryParam.biddingDate_begin">
							</j-date>
							<span class="query-group-split-cust"></span>
							<j-date placeholder="请选择结束" class="query-group-cust" v-model="queryParam.biddingDate_end">
							</j-date>
						</a-form-item>
					</a-col>
					<a-col :span="6">
						<span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
							<a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
							<a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置
							</a-button>
							<!--              <a @click="handleToggleSearch" style="margin-left: 8px">
                {{ toggleSearchStatus ? '收起' : '展开' }}
                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
              </a> -->
						</span>
					</a-col>
				</a-row>
			</a-form>
		</div>
		<!-- 查询区域-END -->

		<!-- 操作按钮区域 -->
		<!--    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('招标主表')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
      <j-super-query :fieldList="superFieldList" ref="superQueryModal" @handleSuperQuery="handleSuperQuery"></j-super-query>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>
    </div> -->

		<!-- table区域-begin -->
		<div>
			<!--      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div> -->

			<a-table ref="table" size="middle" rowKey="id" class="j-table-force-nowrap" :scroll="{x:true}"
				:columns="columns" :dataSource="dataSource" :pagination="ipagination" :loading="loading"
				:rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
				@change="handleTableChange" :customRow="customRow">

				<template slot="htmlSlot" slot-scope="text">
					<div v-html="text"></div>
				</template>
				<template slot="imgSlot" slot-scope="text,record">
					<span v-if="!text" style="font-size: 12px;font-style: italic;">无图片</span>
					<img v-else :src="getImgView(text)" :preview="record.id" height="25px" alt=""
						style="max-width:80px;font-size: 12px;font-style: italic;" />
				</template>
				<template slot="fileSlot" slot-scope="text">
					<span v-if="!text" style="font-size: 12px;font-style: italic;">无文件</span>
					<a-button v-else :ghost="true" type="primary" icon="download" size="small"
						@click="downloadFile(text)">
						下载
					</a-button>
				</template>

				<span slot="action" slot-scope="text, record">
					<div v-if="record.biddingStatusValue!='2'">
					<a @click="handleDetail(record)">查看</a>
					</div>
					<div v-if="record.biddingStatusValue=='2'">
						<a @click="handleEdit(record)">报价</a>
					</div>
				</span>

			</a-table>
		</div>

		<bidding-main-modal ref="modalForm" @ok="modalFormOk" />
	</a-card>
</template>

<script>
	import {
		JeecgListMixin
	} from '@/mixins/JeecgListMixin'
	import BiddingMainModal from './modules/BiddingMainModal'
	import '@/assets/less/TableExpand.less'
	import {
		billListMixin
	} from '../mixins/billListMixin'
	import {
		billModalMixin
	} from '../mixins/billModalMixin'
	import JEllipsis from '@/components/jeecg/JEllipsis'

	export default {
		name: "BiddingMainList",
		mixins: [JeecgListMixin, billListMixin, billModalMixin],
		components: {
			BiddingMainModal,
			JEllipsis
		},
		data() {
			return {
				description: '招标主表管理页面',
				disableMixinCreated: true,
				// 表头
				columns: [
					{
						title: '招标编码',
						align: "center",
						dataIndex: 'biddingNo',
            width: 120,
					},
					{
						title: '招标名称',
						align: "center",
						dataIndex: 'biddingName',
            width: 120,
					},
					{
						title: '招标截至时间',
						align: "center",
						dataIndex: 'biddingDeadline',
						customRender: function(text) {
							return !text ? "" : (text.length > 10 ? text.substr(0, 10) : text)
						},
            width: 120,
					},
					{
						title: '招标项目',
						align: "center",
						dataIndex: 'projectId',
            width: 120,
					},
					{
						title: '招标需求',
						align: "center",
						dataIndex: 'requestId',
            width: 120,
					},
					{
						title: '招标类型',
						align: "center",
						dataIndex: 'biddingType_dictText',
            width: 120,
					},
					{
						title: '招标状态',
						align: "center",
						dataIndex: 'biddingStatus_dictText',
            width: 120,
					},

					{
						title: '开标时间',
						align: "center",
						dataIndex: 'openBiddingDate',
						customRender: function(text) {
							return !text ? "" : (text.length > 10 ? text.substr(0, 10) : text)
						},
            width: 120,
					},
					{
						title: '投标金额',
						align: "center",
						dataIndex: 'finalAmount',
            width: 120,
					},

					{
						title: '操作',
						dataIndex: 'action',
						align: "center",
						fixed: "right",
						width: 100,
						scopedSlots: {
							customRender: 'action'
						},
					}
				],
				url: {
					list: "/srm/biddingMain/list",
					delete: "/srm/biddingMain/delete",
					deleteBatch: "/srm/biddingMain/deleteBatch",
					exportXlsUrl: "/srm/biddingMain/exportXls",
					importExcelUrl: "srm/biddingMain/importExcel",

				},
				dataSource: [
					{biddingNo:'ZB20220616001',
					biddingName:'测试招标项目1601',
					biddingDeadline:'2022-07-01 18:00:00',
					//requestId:'',
					biddingType:'邀请招标',
					biddingStatus:'已报价',
					biddingStatusValue: '4',
					finalAmount: '15,00,000',
					openBiddingDate:'2022-07-10 9:00:00'},
					{biddingNo:'ZB20220616002',
					biddingName:'测试招标项目1602',
					biddingDeadline:'2022-07-05 18:00:00',
					//requestId:'',
					biddingType:'邀请招标',
					biddingStatus:'已接受',
					biddingStatusValue: '2',
					openBiddingDate:'2022-07-10 9:00:00'}
				],
				dictOptions: {},
				superFieldList: [],
			}
		},
		created() {
			this.getSuperFieldList();
		},
		computed: {
			importExcelUrl: function() {
				return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
			}
		},
		methods: {
			customRow(record, index) {
				return {
					// 这个style就是我自定义的属性，也就是官方文档中的props
					style: {
						// 行背景色
						'background-color': index % 2 !== 0 ? '#f0f0f0' : '#fefefe',
						// 字体类型
						'font-family': 'Microsoft YaHei',
					},
				}
			},
			initDictConfig() {},
			getSuperFieldList() {
				let fieldList = [];
				fieldList.push({
					type: 'string',
					value: 'biddingName',
					text: '招标名称',
					dictCode: ''
				})
				fieldList.push({
					type: 'string',
					value: 'biddingNo',
					text: '招标编码',
					dictCode: ''
				})
				fieldList.push({
					type: 'date',
					value: 'biddingDeadline',
					text: '招标截至时间'
				})
				fieldList.push({
					type: 'string',
					value: 'projectId',
					text: '招标项目ID',
					dictCode: ''
				})
				fieldList.push({
					type: 'string',
					value: 'requestId',
					text: '招标需求ID',
					dictCode: ''
				})
				fieldList.push({
					type: 'string',
					value: 'biddingTemplateId',
					text: '评标模板ID',
					dictCode: ''
				})
				fieldList.push({
					type: 'string',
					value: 'biddingType',
					text: '招标类型',
					dictCode: ''
				})
				fieldList.push({
					type: 'string',
					value: 'biddingDescription',
					text: '招标说明',
					dictCode: ''
				})
				fieldList.push({
					type: 'string',
					value: 'biddingStatus',
					text: '招标状态',
					dictCode: ''
				})
				fieldList.push({
					type: 'BigDecimal',
					value: 'depositCash',
					text: '投标保证金',
					dictCode: ''
				})
				fieldList.push({
					type: 'date',
					value: 'depositCashDeadline',
					text: '投标保证金截至时间'
				})
				fieldList.push({
					type: 'string',
					value: 'tenantId',
					text: '租户ID',
					dictCode: ''
				})
				fieldList.push({
					type: 'int',
					value: 'sort',
					text: '排序',
					dictCode: ''
				})
				fieldList.push({
					type: 'string',
					value: 'delFlag',
					text: '删除标志',
					dictCode: ''
				})
				fieldList.push({
					type: 'string',
					value: 'createUser',
					text: '创建用户',
					dictCode: ''
				})
				fieldList.push({
					type: 'string',
					value: 'updateUser',
					text: '更新用户',
					dictCode: ''
				})
				fieldList.push({
					type: 'Text',
					value: 'comment',
					text: '备注',
					dictCode: ''
				})
				fieldList.push({
					type: 'date',
					value: 'openBiddingDate',
					text: '开标时间'
				})
				fieldList.push({
					type: 'date',
					value: 'biddingDate',
					text: '招标时间'
				})
				fieldList.push({
					type: 'string',
					value: 'biddingStartUser',
					text: '招标人',
					dictCode: ''
				})
				fieldList.push({
					type: 'date',
					value: 'winDate',
					text: '中标时间'
				})
				fieldList.push({
					type: 'string',
					value: 'winSupplierId',
					text: '中标供应商',
					dictCode: ''
				})
				fieldList.push({
					type: 'date',
					value: 'voidDate',
					text: '终止或者作废时间'
				})
				fieldList.push({
					type: 'BigDecimal',
					value: 'biddingAmount',
					text: '中标金额',
					dictCode: ''
				})
				fieldList.push({
					type: 'BigDecimal',
					value: 'finalAmount',
					text: '最终金额',
					dictCode: ''
				})
				fieldList.push({
					type: 'string',
					value: 'biddingCurrency',
					text: '报价币种',
					dictCode: ''
				})
				fieldList.push({
					type: 'string',
					value: 'biddingIsIncludeTax',
					text: '是否含税报价',
					dictCode: ''
				})
				this.superFieldList = fieldList
			}
		}
	}
</script>
<style scoped>
	@import '~@assets/less/common.less';
</style>
<style lang="less" scoped>
	.ant-row.ant-form-item {
		margin-bottom: 12px;
	}
</style>
