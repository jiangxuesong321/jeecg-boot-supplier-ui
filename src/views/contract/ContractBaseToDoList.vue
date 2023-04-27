<template>
	<a-card :bordered="false">
		<div class="table-page-search-wrapper">
			<a-form layout="inline" @keyup.enter.native="searchQuery">
				<a-row :gutter="24">
					<a-col :span="6">
						<a-form-item label="合同编码" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
							<j-input placeholder="请输入合同编码" v-model="queryParam.contractNumber"></j-input>
						</a-form-item>
					</a-col>
					<a-col :span="6">
						<a-form-item label="合同名称" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
							<j-input placeholder="请输入合同名称" v-model="queryParam.contractName"></j-input>
						</a-form-item>
					</a-col>
          <a-col :span='6'>
            <a-button type='primary' @click='searchQuery' icon='search'>查询</a-button>
            <a-button @click='searchReset' icon='reload' style='margin-left: 8px' type='primary'>重置</a-button>
          </a-col>
				</a-row>
			</a-form>
		</div>
		<!-- 查询区域-END -->

		<!-- 操作按钮区域 -->
		<div class="table-operator">

		</div>

		<div>

			<a-table ref="table" size="middle" rowKey="id" :scroll="{x:true,y:500}"
				:columns="columns" :dataSource="dataSource" :pagination="ipagination" :loading="loading"
				 @change="handleTableChange"
				:customRow="customRow">

				<span slot="action" slot-scope="text, record">
					<a @click="handleApprove(record)">审核</a>
				</span>

        <template slot='contractNumber' slot-scope='text,record'>
          <span v-if='record.contractStatus == "4"'>
            {{text}}
          </span>
        </template>

			</a-table>
		</div>

		<contract-approve-modal ref="approveForm" @ok="modalFormOk" />
	</a-card>
</template>

<script>
import { JeecgListMixin } from '@/mixins/JeecgListMixin'
import ContractApproveModal from './modules/ContractApproveModal'
import '@/assets/less/TableExpand.less'
import { billListMixin } from '../mixins/billListMixin'
import { billModalMixin } from '../mixins/billModalMixin'
import { getAction } from '@api/manage'
import {
  iegAmount
} from '@/utils/util'
export default {
		name: "ContractBaseList",
		mixins: [JeecgListMixin, billListMixin, billModalMixin],
		components: {
			ContractApproveModal
		},
		data() {
			return {
				description: '合同基本信息表管理页面',
				queryParam: {
					contractNumber: '',
					contractName: '',
					contractType: '',
					createTime_begin: '',
					createTime_end: '',
					isTodo: '1'
				},
				// 表头
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
					// {
					// 	title: '合同编号',
					// 	align: "center",
					// 	dataIndex: 'contractNumber',
					// 	scopedSlots: {
					// 		customRender: 'contractNumber'
					// 	},
          //   width: 140,
					// },
					{
						title: '合同名称',
						align: "center",
						dataIndex: 'contractName',
            width: 160,
					},
					{
						title: '甲方',
						align: "center",
						dataIndex: 'contractFirstParty',
            width: 120,
					},
					{
						title: '乙方',
						align: "center",
						dataIndex: 'contractSecondParty',
            width: 120,
					},
					{
						title: '合同币种',
						align: "center",
						dataIndex: 'contractCurrency_dictText',
            width: 120,
					},
					{
						title: '合同金额(原币)',
						align: "center",
						dataIndex: 'contractAmountTax',
            width: 120,
            customRender:function (t,r,index) {
              let icon = "";
              if(r.contractCurrency == 'RMB'){
                icon = '¥';
              }else if(r.contractCurrency == 'JPY'){
                icon = 'Ұ';
              }else if(r.contractCurrency == 'USD'){
                icon = '$';
              }else if(r.contractCurrency == 'EUR'){
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
						title: '合同金额(本币)',
						align: "center",
						dataIndex: 'contractAmountTaxLocal',
            width: 120,
            customRender:function (t,r,index) {
              let icon = "";
              icon = '¥';
              const obj = {
                children: icon + iegAmount(Math.trunc(t),'total'),
                attrs: {},
              };
              obj.attrs.align = 'right';//控制表体内容居右
              return obj;
            }
					},
					{
						title: '合同状态',
						align: "center",
						dataIndex: 'contractStatus_dictText',
            width: 120,
					},
					{
						title: '合同生成时间',
						align: "createTime",
						dataIndex: 'createTime',
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
					list: "/srm/contractBase/list",
					delete: "/srm/contractBase/delete",
					deleteBatch: "/srm/contractBase/deleteBatch",
					exportXlsUrl: "/srm/contractBase/exportXls",
					importExcelUrl: "srm/contractBase/importExcel",

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
      loadData(arg) {
        if(!this.url.list){
          this.$message.error("请设置url.list属性!")
          return
        }
        //加载数据 若传入参数1则加载第一页的内容
        if (arg === 1) {
          this.ipagination.current = 1;
        }
        var params = this.getQueryParams();//查询条件
        params.isTodo = '0';
        this.loading = true;
        getAction(this.url.list, params).then((res) => {
          if (res.success) {
            //update-begin---author:zhangyafei    Date:20201118  for：适配不分页的数据列表------------
            this.dataSource = res.result.records||res.result;
            if(res.result.total)
            {
              this.ipagination.total = res.result.total;
            }else{
              this.ipagination.total = 0;
            }
            //update-end---author:zhangyafei    Date:20201118  for：适配不分页的数据列表------------
          }else{
            this.$message.warning(res.message)
          }
        }).finally(() => {
          this.loading = false
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
			handleApprove(record) {
				this.$refs.approveForm.edit(record)
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
