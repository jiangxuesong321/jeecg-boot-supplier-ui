<template>
	<a-card :bordered="false">
		<!-- 查询区域 -->
		<div class="table-page-search-wrapper">
			<a-form layout="inline" @keyup.enter.native="searchQuery">
				<a-row :gutter="24">
					<a-col :span="6">
						<a-form-item label="申请单号" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
							<j-input placeholder="请输入收款申请编号" v-model="queryParam.applyCode"></j-input>
						</a-form-item>
					</a-col>
					<a-col :span="6">
						<a-form-item label="合同名称" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
							<j-input placeholder="请输入合同名称" v-model="queryParam.contractName"></j-input>
						</a-form-item>
					</a-col>
					<a-col :span="6">
						<a-form-item label="收款方式" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
							<j-dict-select-tag placeholder="请选择收款方式" v-model="queryParam.payMethod"
								dictCode="payMethod" />
						</a-form-item>
					</a-col>
          <a-col :span="6">
            <a-form-item label="收款类型" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
              <j-dict-select-tag placeholder="请选择收款类型" v-model="queryParam.payType"
                                 dictCode="payType" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <span style='float: right;overflow: hidden;' class='table-page-search-submitButtons'>
							<a-button type='primary' @click='searchQuery' icon='search'>查询</a-button>
							<a-button type='primary' @click='searchReset' icon='reload' style='margin-left: 8px'>重置</a-button>
              <a-button type='primary' @click='handleAdd' icon='plus' style='margin-left: 8px'>收款申请</a-button>
						</span>
          </a-col>
				</a-row>
			</a-form>
		</div>
		<!-- 操作按钮区域 -->
		<div class="table-operator">

		</div>

		<div>

			<a-table ref="table" size="middle" bordered rowKey="id" :scroll="{x:true,y:500}"
				:columns="columns" :dataSource="dataSource" :pagination="ipagination" :loading="loading"
				@change="handleTableChange" :customRow="customRow">

        <template slot='contractName' slot-scope='text,record'>
          <div v-if='text != null && text != "" && text != undefined'>
            <div v-if="text.split('-') != null && text.split('-').length > 0 " style="width: 260px;height: auto;word-wrap: break-word;overflow: hidden; text-overflow: ellipsis;white-space: nowrap;"
                 :title="text.split('-')[0]">
              {{text.split('-')[0]}}
            </div>
            <div v-if="text.split('-') != null && text.split('-').length > 1" style="width: 260px;height: auto;word-wrap: break-word;overflow: hidden; text-overflow: ellipsis;white-space: nowrap;"
                 :title="text.split('-')[1]">
              {{text.split('-')[1]}}
            </div>
          </div>

        </template>

				<span slot="action" slot-scope="text, record">
					<a @click="handleEdit(record)" v-if='record.applyStatus == "00" || record.applyStatus == "20"'>编辑</a>
					<a-divider type="vertical" v-if='record.applyStatus == "00"  || record.applyStatus == "20"'/>
					<a @click="handleDelete(record.id)" v-if='record.applyStatus == "00"'>删除</a>
          <a-divider type="vertical" v-if='record.applyStatus == "00"'/>
					<a @click="handleDetail(record)">查看</a>
				</span>

			</a-table>
		</div>

		<pur-pay-apply-modal ref="modalForm" @ok="modalFormOk" />
	</a-card>
</template>

<script>
	import {
		JeecgListMixin
	} from '@/mixins/JeecgListMixin'
	import PurPayApplyModal from './modules/PurPayApplyModal'
	import '@/assets/less/TableExpand.less'
	import { billListMixin } from '../mixins/billListMixin'
	import { billModalMixin } from '../mixins/billModalMixin'
  import { deleteAction, httpAction } from '@api/manage'
  import { iegAmount } from '@/utils/util'

	export default {
		name: "PurPayApplyList",
		mixins: [JeecgListMixin, billListMixin, billModalMixin],
		components: {
			PurPayApplyModal
		},
		data() {
			return {
				description: '收款申请管理页面',
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
					// {
					// 	title: '收款申请单号',
					// 	align: "center",
					// 	dataIndex: 'applyCode',
          //   width: 140,
					// },
					{
						title: '合同编号',
						align: "center",
						dataIndex: 'contractNumber',
            width: 140,
					},
					{
						title: '合同名称',
						align: "center",
						dataIndex: 'contractName',
            width: 260,
            scopedSlots: {
              customRender: 'contractName'
            },
					},
					{
						title: '币种',
						align: "center",
						dataIndex: 'currency_dictText',
            width: 120,
					},
					{
						title: '支付方式',
						align: "center",
						dataIndex: 'payMethod_dictText',
            width: 120,
					},
					{
						title: '收款类型',
						align: "center",
						dataIndex: 'payType_dictText',
            width: 120,
					},
					{
						title: '收款比例(%)',
						align: "center",
						dataIndex: 'payRate',
            width: 120,
					},
          {
            title: '申请收款金额',
            align: "center",
            dataIndex: 'payAmountOther',
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
						title: '申请时间',
						align: "center",
						dataIndex: 'applyTime',
						customRender: function(text) {
							return !text ? "" : (text.length > 10 ? text.substr(0, 10) : text)
						},
            width: 120,
					},
          {
            title: '申请状态',
            align: "center",
            dataIndex: 'applyStatus_dictText',
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
				url: {
					list: "/srm/purPayApply/list",
          delete:'/srm/purPayApply/delete',
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
			}
		},
		methods: {
      handleDelete: function (id) {
        var that = this;
        that.$confirm({
          content: `是否确认提交?`,
          onOk: () => {
            that.confirmLoading = true;
            deleteAction(that.url.delete, {id: id}).then((res) => {
              if (res.success) {
                //重新计算分页问题
                that.reCalculatePage(1)
                that.$message.success(res.message);
                that.loadData();
              } else {
                that.$message.warning(res.message);
              }
            });
          }
        })

      },
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