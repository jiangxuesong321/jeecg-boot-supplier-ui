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
	:headerStyle="{ textAlign: 'center' }"
    @close="close"
    destroyOnClose
    :visible="visible">
    <contract-base-form ref="realForm" @ok="submitCallback" :disabled="disableSubmit"/>
    <div class="drawer-footer" style="text-align: center;margin-top:15px;">
      <a-button @click="handleCancel" style="margin-bottom: 0;">关闭</a-button>
      <a-button v-if="!disableSubmit"  @click="handleOk" type="primary" style="margin-bottom: 0;">提交</a-button>
    </div>
  </a-drawer>
<!--  </j-modal> -->
</template>

<script>

  import ContractBaseForm from './ContractBaseForm'

  export default {
    name: 'ContractBaseModal',
    components: {
      ContractBaseForm
    },
    data() {
      return {
        title:'合同',
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