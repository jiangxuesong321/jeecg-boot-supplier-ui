<template>
	<a-drawer :title="title" :width="width" placement="right" :closable="false" @close="close" destroyOnClose
		:visible="visible">
		<inovice-form ref="realForm" @ok="submitCallback" :disabled="disableSubmit"></inovice-form>
		<div class="drawer-footer" style="text-align: center;">
			<a-button @click="handleCancel" style="margin-bottom: 0;" type="primary">关闭</a-button>
      <a-button v-if="!disableSubmit" @click="handleDraft" type="primary" style="margin-bottom: 0;margin-left:5px;">草稿</a-button>
			<a-button v-if="!disableSubmit" @click="handleOk" type="primary" style="margin-bottom: 0;margin-left:5px;">提交</a-button>
		</div>
	</a-drawer>
</template>

<script>
	import InoviceForm from './InoviceForm'
	export default {
		name: 'PurchasePayInoviceModal',
		components: {
			InoviceForm
		},
		data() {
			return {
				title: '',
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
        this.$refs.realForm.submitDraft();
      },
			handleOk() {
				this.$refs.realForm.submitForm();
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
