<template>
<!--  <j-modal-->
<!--    :title="title"-->
<!--    :width="width"-->
<!--    :visible="visible"-->
<!--    switchFullscreen-->
<!--    @ok="handleOk"-->
<!--    :okButtonProps="{ class:{'jee-hidden': disableSubmit} }"-->
<!--    @cancel="handleCancel"-->
<!--    cancelText="关闭">-->
<!--    <send-goods-form ref="realForm" @ok="submitCallback" :disabled="disableSubmit"></send-goods-form>-->
<!--  </j-modal>-->
  <a-drawer :title="title" :width="width" placement="right" :closable="false" :headerStyle="{ textAlign: 'center'}"
            @close="handleCancel" destroyOnClose :visible="visible">
    <send-goods-form ref="realForm" @ok="submitCallback" :disabled="disableSubmit"></send-goods-form>
    <div class="drawer-footer" style="text-align: center;margin-top:15px;">
<!--      <a-button  @click="pdfDownload" style="margin-left:10px;" type="primary" ghost v-if='record.sendStatus == "2"'>下载PDF</a-button>-->
      <a-button  @click="handleOk" type="primary" v-if="!disableSubmit" style="margin-left:10px;">提交</a-button>
      <a-button key="cancel" @click="handleCancel" style="margin-left:10px;" type="primary" ghost>取消</a-button>
    </div>
  </a-drawer>
</template>

<script>

  import SendGoodsForm from './SendGoodsForm'
  export default {
    name: 'SendGoodsModal',
    components: {
      SendGoodsForm
    },
    data () {
      return {
        record:{},
        title:'',
        width:'90%',
        visible: false,
        disableSubmit: false
      }
    },
    methods: {
      pdfDownload() {
        this.$refs.realForm.pdfDownload();
      },
      add () {
        this.record = {};
        this.visible=true
        this.$nextTick(()=>{
          this.$refs.realForm.add();
        })
      },
      edit (record) {
        this.record = record;
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
        this.$refs.realForm.submitForm();
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