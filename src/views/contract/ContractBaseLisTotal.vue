<template>
	<a-card :bordered="false">
		<div>
			<a-tabs defaultActiveKey="1" @change="callback">
				<a-tab-pane tab="待审合同" key="1">
					<contract-base-to-do-list ref="todoList"></contract-base-to-do-list>
				</a-tab-pane>
				<a-tab-pane tab="合同列表" key="2">
					<contract-base-list ref="historyList"></contract-base-list>
				</a-tab-pane>
        <a-tab-pane tab="合同执行进度" key="3">
          <contract-report-list ref='report'></contract-report-list>
        </a-tab-pane>
			</a-tabs>
		</div>
	</a-card>
</template>

<script>
	import '@/assets/less/TableExpand.less'
	import ContractBaseToDoList from './ContractBaseToDoList'
	import ContractBaseList from './ContractBaseList'
  import ContractReportList from '@views/contract/ContractReportList'

	export default {
		name: "ContractBaseLisTotal",
		components: {
			ContractBaseToDoList,
			ContractBaseList,
      ContractReportList
		},
		data() {
			return {
				description: '合同管理页面',
				tabKey: "1",
			}
		},
		created() {
		},
		methods: {
			callback(key){
			  let that=this;
			},
			initDictConfig() {},
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