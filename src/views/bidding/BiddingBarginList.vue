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

		<div>
			<a-table ref="table" size="middle" rowKey="id"  :scroll="{x:true}"
				:columns="columns" :dataSource="dataSource" :pagination="ipagination" :loading="loading"
				@change="handleTableChange" >

				<span slot="action" slot-scope="text, record">
					<a @click="handleBargin(record)" v-if='record.isBargin == "1" || record.isBargin == "3" '>议价</a>
				</span>
			</a-table>
		</div>

		<bidding-bargin-modal ref='modalForm' @ok='toBack'></bidding-bargin-modal>
	</a-card>
</template>

<script>
import { JeecgListMixin } from '@/mixins/JeecgListMixin'
import '@/assets/less/TableExpand.less'
import { billListMixin } from '../mixins/billListMixin'
import { billModalMixin } from '../mixins/billModalMixin'
import JEllipsis from '@/components/jeecg/JEllipsis'
import { getAction } from '@api/manage'
import BiddingBarginModal from '@views/bidding/modules/BiddingBarginModal'

export default {
		name: "BiddingBarginList",
		mixins: [JeecgListMixin, billListMixin, billModalMixin],
		components: {
			JEllipsis,
      BiddingBarginModal
		},
		data() {
			return {
				description: '招标主表管理页面',
				disableMixinCreated: true,
				// 表头
				columns: [
          {
            title: '序号',
            dataIndex: '',
            key: 'rowIndex',
            width: 60,
            align: 'center',
            customRender: function(t, r, index) {
              return parseInt(index) + 1
            }
          },
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
            title: '标的名称',
            align: "center",
            dataIndex: 'prodName',
            width: 120,
          },
          {
            title: '标的编码',
            align: "center",
            dataIndex: 'prodCode',
            width: 120,
          },
          {
            title: '报价币种',
            align: "center",
            dataIndex: 'currency_dictText',
            width: 120,
          },
          {
            title: '报价单价',
            align: "center",
            dataIndex: 'priceTax',
            width: 120,
          },
          {
            title: '标的数量',
            align: "center",
            dataIndex: 'qty',
            width: 120,
          },
          {
            title: '报价总额',
            align: "center",
            dataIndex: 'amountTax',
            width: 120,
          },
					{
						title: '招标状态',
						align: "center",
						dataIndex: 'biddingStatus',
            width: 120,
            customRender: function(text) {
						  return '已中标';
            }
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
					list: "/srm/biddingMain/fetchBarginList",
				},
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
      toBack(){
        this.searchQuery();
      },
      handleBargin(record){
        this.$refs.modalForm.edit(record);
      },
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
