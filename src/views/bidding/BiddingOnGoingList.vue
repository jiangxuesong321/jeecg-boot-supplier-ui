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
						<span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
							<a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
							<a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置
							</a-button>
						</span>
					</a-col>
				</a-row>
			</a-form>
		</div>
		<!-- 查询区域-END -->

		<div>

			<a-table ref="table" size="middle" rowKey="id" class="j-table-force-nowrap" :scroll="{x:true,y:500}"
				:columns="columns" :dataSource="dataSource" :pagination="ipagination" :loading="loading" :customRow="customRow"
				@change="handleTableChange">


				<span slot="action" slot-scope="text, record">
					<div v-if="record.biddingStatusValue=='1'">
					<a-popconfirm title="确定接受吗?" @confirm="() => handleAccept(record)">
						<a>接受</a>
					</a-popconfirm>
					<a-divider type="vertical" />
					<a-popconfirm title="确定放弃吗?" @confirm="() => handleReject(record.id)">
						<a>放弃</a>
					</a-popconfirm>
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
						scopedSlots: {
							customRender: 'biddingNo'
						},
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
						title: '操作',
						dataIndex: 'action',
						align: "center",
						width: 147,
						scopedSlots: {
							customRender: 'action'
						},
					}
				],
				dataSource: [

				],
				url: {
					list: "/srm/biddingMain/list",
					delete: "/srm/biddingMain/delete",
					deleteBatch: "/srm/biddingMain/deleteBatch",
					exportXlsUrl: "/srm/biddingMain/exportXls",
					importExcelUrl: "srm/biddingMain/importExcel",

				},
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
			handleAccept(record) {
				record.biddingStatusValue = '2'
				record.biddingStatus = '已接受'
			},
			handleReject(record) {
				record.biddingStatusValue = '3'
				record.biddingStatus = '已放弃'
			},

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
