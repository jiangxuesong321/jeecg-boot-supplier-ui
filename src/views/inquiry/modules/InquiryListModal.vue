<template>
<!--  <j-modal
    :title="title"
    :width="1200"
    :visible="visible"
    :maskClosable="false"
    switchFullscreen
    @ok="handleOk"
    :okButtonProps="{ class:{'jee-hidden': disableSubmit} }"
    @cancel="handleCancel"> -->
	<a-drawer
	  :title="title"
	  :width="width"
	  placement="right"
	  :closable="false"
	  :headerStyle="{ textAlign: 'center'}"
	  @close="close"
	  destroyOnClose
	  :visible="visible">
		<quotation-main-form ref="realForm" @ok="submitCallback" :disabled="disableSubmit"></quotation-main-form>
		<div class="drawer-footer" style="text-align: center;margin-top:15px;">
			<a-button key="approved" @click="handleOk" type="primary" v-if="!disableSubmit">提交</a-button>
			<a-button key="cancel" @click="handleCancel" style="margin-left:10px;" type="primary" ghost>取消</a-button>
		</div>
	</a-drawer>
  <!-- </j-modal> -->
</template>

<script>

  import InquiryListForm from './InquiryListForm'
  import QuotationMainForm from './QuotationMainForm'
  export default {
    name: 'InquiryListModal',
    components: {
      InquiryListForm,
	  QuotationMainForm
    },
    data() {
      return {
        title:'询价',
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
      bargain(record){
        this.visible=true
        this.$nextTick(()=>{
          this.$refs.realForm.bargain(record);
        })
      },
      editTwo(record){
        this.visible=true
        this.$nextTick(()=>{
          this.$refs.realForm.editTwo(record);
        })
      },
      detail(record){
        this.visible=true
        this.$nextTick(()=>{
          this.$refs.realForm.detail(record);
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

<style lang="less" scoped>
/** Button按钮间距 */
  .ant-btn {
    margin-left: 30px;
    margin-bottom: 30px;
  }
  .drawer-footer{
    position: absolute;
    bottom: -8px;
    width: 100%;
    border-top: 1px solid #e8e8e8;
    padding: 10px 16px;
    text-align: center;
    left: 0;
    background: #fff;
    border-radius: 0 0 2px 2px;
  }
</style>