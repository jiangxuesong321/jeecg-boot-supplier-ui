<template>
	<a-card :bordered="false">
		<!-- 查询区域 -->
<!--		<div class="card-title">-->
<!--			发票管理-->
<!--		</div>-->
		<div class="table-page-search-wrapper">
			<a-form layout="inline" @keyup.enter.native="searchQuery">
				<a-row :gutter="24">
					<a-col :span="6">
						<a-form-item label="发票编号" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
							<j-input placeholder="请输入发票编号" v-model="queryParam.invoiceNo"></j-input>
						</a-form-item>
					</a-col>
					<a-col :span="6">
						<a-form-item label="状态" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
              <j-dict-select-tag v-model='queryParam.status' dict-code='invoice_status'></j-dict-select-tag>
						</a-form-item>
					</a-col>
					<a-col :span="6">
						<a-form-item label="合同名称" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
							<j-input placeholder="请输入合同名称" v-model="queryParam.contractName"></j-input>
						</a-form-item>
					</a-col>
					<a-col :span="6">
						<span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
							<a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
							<a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px" ghost>重置
							</a-button>
						</span>
					</a-col>
				</a-row>
			</a-form>
		</div>
		<!-- 查询区域-END -->

		<!-- 操作按钮区域 -->
		<div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">登记发票</a-button>
		</div>
		<div>

			<a-table ref="table" size="middle" :scroll="{x:true, y:500}" rowKey="id" :columns="columns"
				:dataSource="dataSource" :pagination="ipagination" :loading="loading"
				 @change="handleTableChange" :customRow="customRow">

        <template slot='contractName' slot-scope='text,record'>
          <span v-if='text != null && text != undefined && text != "" && text.indexOf("-") > -1 && text.split("-").length > 2'>
            {{text.split("-")[1]}} - {{text.split("-")[2]}}
          </span>
          <span v-else>
            {{text}}
          </span>
        </template>

				<span slot="action" slot-scope="text, record">
					<a @click="handleEdit(record)" v-if='record.status == "2" || record.status == "0"'>编辑</a>
					<a-divider type="vertical" v-if='record.status == "2" || record.status == "0"'/>
          <a @click="handleDetail(record)">详情</a>
          <a-divider type="vertical"/>
          <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)" >
            <a>删除</a>
          </a-popconfirm>
				</span>
			</a-table>

		</div>

		<inovice-modal ref="modalForm" @ok="modalFormOk"></inovice-modal>
	</a-card>
</template>

<script>
	import '@/assets/less/TableExpand.less'
	import {
		mixinDevice
	} from '@/utils/mixin'
	import {
		JeecgListMixin
	} from '@/mixins/JeecgListMixin'
	import InoviceModal from './modules/InoviceModal'
	import {
		billListMixin
	} from '../mixins/billListMixin'
	import {
		billModalMixin
	} from '../mixins/billModalMixin'
	import ListColumnsSetter from '@views/components/ListColumnsSetter'
  import { iegAmount } from '@/utils/util'

	export default {
		name: 'PurchasePayInoviceList',
		mixins: [JeecgListMixin, mixinDevice, billListMixin, billModalMixin],
		components: {
			InoviceModal,
			ListColumnsSetter
		},
		data() {
			return {
				description: '发票登记管理页面',
				// 表头
				columns: [
				  {
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
						title: '合同名称',
						align: "center",
						dataIndex: 'contractName',
            width: 180,
            scopedSlots: {
              customRender: 'contractName'
            }
					},
          {
            title: '合同编码',
            align: "center",
            dataIndex: 'contractNumber',
            width: 180,
          },
					{
						title: '发票类型',
						align: "center",
						dataIndex: 'invoiceType_dictText',
            width: 120,
					},
					{
						title: '币种',
						align: "center",
						dataIndex: 'currency_dictText',
            width: 120,
					},
					{
						title: '发票号码',
						align: "center",
						dataIndex: 'invoiceNo',
            width: 120,
					},
					{
						title: '开票金额（未税）',
						align: "center",
						dataIndex: 'invoiceAmount',
            width: 120,
            customRender:function (t,r,index) {
              let icon = "";
              if(r.currency == 'RMB'){
                icon = '¥';
              }else if(r.currency == 'JPY'){
                icon = 'Ұ';
              }else if(r.currency == 'USD'){
                icon = '$';
              }else if(r.currency == 'EUR'){
                icon = '€';
              }
              // return icon + iegAmount(t,'total')
              const obj = {
                children: icon + iegAmount(Math.trunc(t),'total'),
                attrs: {},
              };
              obj.attrs.align = 'right';//控制表体内容居右
              return obj;
            }
					},
					{
						title: '税额',
						align: "center",
						dataIndex: 'invoiceTax',
            width: 120,
            customRender:function (t,r,index) {
              let icon = "";
              if(r.currency == 'RMB'){
                icon = '¥';
              }else if(r.currency == 'JPY'){
                icon = 'Ұ';
              }else if(r.currency == 'USD'){
                icon = '$';
              }else if(r.currency == 'EUR'){
                icon = '€';
              }
              // return icon + iegAmount(t,'total')
              const obj = {
                children: icon + iegAmount(Math.trunc(t),'total'),
                attrs: {},
              };
              obj.attrs.align = 'right';//控制表体内容居右
              return obj;
            }
					},
					{
						title: '开票金额（含税）',
						align: "center",
						dataIndex: 'invoiceAmountTax',
            width: 120,
            customRender:function (t,r,index) {
              let icon = "";
              if(r.currency == 'RMB'){
                icon = '¥';
              }else if(r.currency == 'JPY'){
                icon = 'Ұ';
              }else if(r.currency == 'USD'){
                icon = '$';
              }else if(r.currency == 'EUR'){
                icon = '€';
              }
              // return icon + iegAmount(t,'total')
              const obj = {
                children: icon + iegAmount(Math.trunc(t),'total'),
                attrs: {},
              };
              obj.attrs.align = 'right';//控制表体内容居右
              return obj;
            }
					},
					{
						title: '开票日期',
						align: "center",
						dataIndex: 'invoiceDate',
						customRender: function(text) {
							return !text ? "" : (text.length > 10 ? text.substr(0, 10) : text)
						},
            width: 120,
					},
          {
            title: '状态',
            align: "center",
            dataIndex: 'status_dictText',
            width: 120,
          },
					{
						title: '操作',
						dataIndex: 'action',
						align: "center",
						width: 147,
						scopedSlots: {
							customRender: 'action'
						}
					}
				],
				url: {
					list: "/srm/purchasePayInovice/list",
					delete: "/srm/purchasePayInovice/delete",
					deleteBatch: "/srm/purchasePayInovice/deleteBatch",
					exportXlsUrl: "/srm/purchasePayInovice/exportXls",
					importExcelUrl: "srm/purchasePayInovice/importExcel",

				},
				dictOptions: {},
				superFieldList: [],
			}
		},
		created() {

		},
		computed: {
			importExcelUrl: function() {
				return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
			},
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
<style>
	.ant-table {
		margin-top:40px;
	}
</style>
