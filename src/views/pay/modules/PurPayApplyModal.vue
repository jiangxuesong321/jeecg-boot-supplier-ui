<template>
	<a-drawer title="收款申请" :width="width" placement="right" :closable="false" :headerStyle="{ textAlign: 'center'}"
		@close="close" destroyOnClose :visible="visible">
		<pur-pay-apply-form ref="realForm" @ok="submitCallback" :disabled="disableSubmit" />
		<div class="drawer-footer" style="text-align: center;margin-top:15px;">
      <a-button  @click="handleDraft" type="primary" v-if="!disableSubmit" >保存草稿</a-button>
      <a-button  @click="handleOk" type="primary" v-if="!disableSubmit" style="margin-left:10px;">提交</a-button>
			<a-button key="cancel" @click="handleCancel" style="margin-left:10px;" type="primary" ghost>取消</a-button>
		</div>
	</a-drawer>
	<!--  </j-modal> -->
</template>

<script>
	import PurPayApplyForm from './PurPayApplyForm'

	export default {
		name: 'PurPayApplyModal',
		components: {
			PurPayApplyForm
		},
		data() {
			return {
				title: '收款申请',
				width: '90%',
				visible: false,
				disableSubmit: false
			}
		},
		methods: {
			add() {
				this.visible = true
				this.$nextTick(() => {
					this.$refs.realForm.add();
				})
			},
			edit(record) {
				this.visible = true
				this.$nextTick(() => {
					this.$refs.realForm.edit(record);
				})
			},
			close() {
				this.$emit('close');
				this.visible = false;
			},
      handleDraft(){
        this.$refs.realForm.handleDraft();
      },
			handleOk() {
				this.$refs.realForm.handleOk();
			},
			submitCallback() {
				this.$emit('ok');
				this.visible = false;
			},
			handleCancel() {
				this.close()
			}
		}
	}
</script>

<style scoped>
</style>
