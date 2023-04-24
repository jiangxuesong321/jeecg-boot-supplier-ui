<template>
	<a-card :bordered="false">
<!--		<div class="card-title">-->
<!--			询价管理-->
<!--		</div>-->
		<div>
			<a-tabs defaultActiveKey="1" @change="callback">
				<a-tab-pane tab="进行中" key="1">
					<inquiry-on-going ref="todoList"></inquiry-on-going>
				</a-tab-pane>
				<a-tab-pane tab="询价历史" key="2">
					<inquiry-list ref="historyList" tabKey='2'></inquiry-list>
				</a-tab-pane>
        <a-tab-pane tab="中标再议价" key="3">
          <inquiry-list ref="biddingList" tabKey='3'></inquiry-list>
        </a-tab-pane>
			</a-tabs>
		</div>
	</a-card>
</template>

<script>
	import '@/assets/less/TableExpand.less'
	import InquiryOnGoing from './InquiryOnGoing'
	import InquiryList from './InquiryList'

	export default {
		name: "InquiryListTotal",
		components: {
			InquiryOnGoing,
			InquiryList
		},
		data() {
			return {
				description: '报价管理页面',
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