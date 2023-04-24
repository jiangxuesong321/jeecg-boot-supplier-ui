<template>
  <a-drawer
    :title="title"
    :width="width"
    placement="right"
    :closable="false"
	:headerStyle="{ textAlign: 'center'}"
    @close="close"
    destroyOnClose
    :visible="visible">
		<div>
      <bidding-main-form ref="realForm" @ok="submitCallback" :disabled="disableSubmit"/>
		</div>
		<div class="drawer-footer" style="text-align: center;margin-top:15px;">
			<a-button key="approved" @click="handleOk" type="primary" v-if="!disableSubmit">提交报价</a-button>
			<a-button key="cancel" @click="handleCancel" style="margin-left:10px;" type="primary" ghost>取消</a-button>
		</div>
	</a-drawer>
</template>

<script>

  import BiddingMainForm from './BiddingMainForm'
  import QuotationMainForm from './QuotationMainForm'

  export default {
    name: 'BiddingMainModal',
    components: {
      BiddingMainForm,
	  QuotationMainForm
    },
    data() {
      return {
        title:'招标',
        width: '90%',
        visible: false,
        disableSubmit: false
      }
    },
    methods:{
      add () {
        this.visible=true
        this.$nextTick(()=>{
          this.$refs.realForm.add();
        })
      },
      edit (record) {
        this.visible=true
        this.$nextTick(()=>{
          this.$refs.realForm.edit(record);
        })
      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        this.$refs.realForm.handleOk();
      },
      submitCallback(){
        this.$emit('ok');
        this.visible = false;
      },
      handleCancel () {
        this.close()
      }
    }
  }
</script>

<style scoped>
</style>