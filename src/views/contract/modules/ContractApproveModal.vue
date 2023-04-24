<template>
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
      <a-button @click="handleCancel" style="margin-bottom: 0;" type='primary'>关闭</a-button>
      <a-button @click="handleOk" type="primary" style="margin-bottom: 0;margin-left:5px;" v-if='!disableSubmit'>提交</a-button>
<!--      <a-button @click="handleReject" type="primary" style="margin-bottom: 0;margin-left:5px;" v-if='!disableSubmit'>驳回</a-button>-->
    </div>
  </a-drawer>
<!--  </j-modal> -->
</template>

<script>
	import {
		billListMixin
	} from '../../mixins/billListMixin'
	import {
		billModalMixin
	} from '../../mixins/billModalMixin'
	import ContractBaseForm from './ContractBaseForm'

  export default {
    name: 'ContractBaseModal',
	mixins: [billListMixin,billModalMixin],
    components: {
      ContractBaseForm
    },
    data() {
      return {
        title:'合同',
        width: '90%',
        visible: false,
        disableSubmit: false,
        model: {
          approveStatus: '2',
          approveComment: ''
        }
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
        this.disableSubmit = false;
        this.visible=true
        this.$nextTick(()=>{
          this.$refs.realForm.editTwo(record);
        })
      },
      view (record) {
        this.disableSubmit = true;
        this.visible=true
        this.$nextTick(()=>{
          this.$refs.realForm.editTwo(record);
        })
      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleReject(){
        this.$refs.realForm.handleReject();
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