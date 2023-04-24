<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :span="6">
            <a-form-item label="申请单号" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
              <j-input placeholder="请输入申请编号" v-model="queryParam.billNo"></j-input>
            </a-form-item>
          </a-col>
          <a-col :span="6">
            <a-form-item label="合同名称" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
              <j-input placeholder="请输入合同名称" v-model="queryParam.contractName"></j-input>
            </a-form-item>
          </a-col>
          <a-col :span="6">
            <a-form-item label="合同编码" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
              <j-input placeholder="请输入合同编码" v-model="queryParam.contractNumber"></j-input>
            </a-form-item>
          </a-col>
          <a-col :span="6">
            <a-form-item label="申请状态" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
              <a-select v-model='queryParam.sendStatus'>
                <a-select-option value='0'>待审核</a-select-option>
                <a-select-option value='1'>驳回</a-select-option>
                <a-select-option value='2'>审核通过</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <span style='float: right;overflow: hidden;' class='table-page-search-submitButtons'>
							<a-button type='primary' @click='searchQuery' icon='search'>查询</a-button>
							<a-button type='primary' @click='searchReset' icon='reload' style='margin-left: 8px'>重置</a-button>
              <a-button type='primary' @click='handleAdd' icon='plus' style='margin-left: 8px'>发货申请</a-button>
						</span>
          </a-col>
        </a-row>
      </a-form>
    </div>

    <!-- table区域-begin -->
    <div>
      <a-table
        ref="table"
        size="middle"
        :scroll="{x:1200,y:500}"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        @change="handleTableChange">
        <template slot='billNo' slot-scope='text,record'>
          <a @click='downLoadPdf(record)'>{{text}}</a>
        </template>


        <span slot="action" slot-scope="text, record">
          <span>
            <a @click="handleEdit(record)" v-if='record.sendStatus == "1"'>编辑</a>
<!--            <a @click="handleDetail(record)" v-else>查看</a>-->
          </span>
          <span v-if='record.sendStatus == "2"'>
            <a-divider type="vertical" />
            <a @click='uploadFile(record)'>回传签收单</a>
            <a-divider type="vertical" />
            <a @click='downLoadPdf(record)'>下载发货单</a>
            <a-divider type="vertical" />
            <a @click='handleFast(record)'>维护承运信息</a>
          </span>

        </span>

      </a-table>
    </div>

    <send-goods-modal ref="modalForm" @ok="modalFormOk"></send-goods-modal>
    <send-goods-pdf ref='modalPdf'></send-goods-pdf>

    <j-modal
      title="附件管理"
      width="30%"
      :visible="visible1"
      :maskClosable="false"
      switchFullscreen
      :fullscreen="false"
      @cancel="handleClose">
      <template slot="footer">
        <a-button key="submit" @click="handleClose" type="primary" >取消</a-button>
        <a-button key="submit" @click="handleSubmit" type="primary" >提交</a-button>
      </template>
      <j-upload v-model="ruleForm.attachment" :trigger-change="true" ></j-upload>
    </j-modal>

    <j-modal
      title="承运信息"
      width="30%"
      :visible="visible2"
      :maskClosable="false"
      switchFullscreen
      :fullscreen="false"
      @cancel="handleClose2">
      <template slot="footer">
        <a-button key="submit" @click="handleClose2" type="primary" >取消</a-button>
        <a-button key="submit" @click="doSubmit" type="primary" >提交</a-button>
      </template>

      <a-form-model ref="form" :model="ruleForm">
        <a-row>
          <a-col :span="24">
            <a-form-model-item label="承运单位" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
              <a-input v-model="ruleForm.fastUnit" placeholder="请输入承运单位" style="width: 100%" />
            </a-form-model-item>
          </a-col>

          <a-col :span="24">
            <a-form-model-item label="承运联系人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
              <a-input v-model="ruleForm.fastUser" placeholder="请输入承运联系人" style="width: 100%" />
            </a-form-model-item>
          </a-col>
        </a-row>
        <a-row>
          <a-col :span="24">
            <a-form-model-item label="承运人联系方式" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="receiveUserTel">
              <a-input v-model="ruleForm.fastUserTel" placeholder="请输入承运人联系方式" style="width: 100%" />
            </a-form-model-item>
          </a-col>
        </a-row>
      </a-form-model>

    </j-modal>
  </a-card>
</template>

<script>

  import '@/assets/less/TableExpand.less'
  import { mixinDevice } from '@/utils/mixin'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import { billListMixin } from '../mixins/billListMixin'
  import { billModalMixin } from '../mixins/billModalMixin'
  import SendGoodsModal from './modules/SendGoodsModal'
  import SendGoodsPdf from '@views/stk/modules/SendGoodsPdf'
  import { putAction } from '@/api/manage'
  export default {
    name: 'SendGoodsList',
    mixins:[JeecgListMixin, mixinDevice,billListMixin, billModalMixin],
    components: {
      SendGoodsModal,
      SendGoodsPdf
    },
    data () {
      return {
        visible2:false,
        visible1:false,
        ruleForm:{
          attachment:'',
          fastUnit:'',
          fastUser:'',
          fastUserTel:''
        },
        description: 'SendGoodsList',
        // 表头
        columns: [
          {
            title: '序号',
            dataIndex: '',
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
          },
          {
            title:'发货申请单号',
            align:"center",
            dataIndex: 'billNo',
            width:160,
            scopedSlots: { customRender: 'billNo' }
          },
          {
            title:'合同名称',
            align:"center",
            dataIndex: 'contractName',
            ellipsis:true,
            width:180,
          },
          {
            title:'合同编码',
            align:"center",
            dataIndex: 'contractNumber',
            width:180,
          },
          {
            title:'申请状态',
            align:"center",
            dataIndex: 'sendStatus',
            width:120,
            customRender:function (t,r,index) {
               if(t == '0'){
                 return '待审核';
               }else if(t == '1'){
                 return '驳回';
               }else if(t == '2'){
                 return '审批通过';
               }
            }
          },
          {
            title:'发货时间',
            align:"center",
            dataIndex: 'sendTime',
            width:120,
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            width:125,
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/srm/stkIoBill/list",
          delete: "/srm/stkIoBill/delete",
          deleteBatch: "/srm/stkIoBill/deleteBatch",
          exportXlsUrl: "/srm/stkIoBill/exportXls",
          importExcelUrl: "srm/stkIoBill/importExcel",

        },
        dictOptions:{},
        superFieldList:[],
      }
    },
    created() {
    this.getSuperFieldList();
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      },
    },
    methods: {
      doSubmit(){
        let url = "/srm/stkIoBill/editFast";
        let that = this;
        that.$confirm({
          content: `是否确认提交?`,
          onOk: () => {
            putAction(url,that.ruleForm).then(res => {
              if(res.success){
                that.$message.success("提交成功");
                that.handleClose2();
                that.searchQuery();
              }else{
                that.$message.error("提交失败");
              }
            })
          }
        })
      },
      handleClose2(){
        this.visible2 = false;
      },
      handleFast(record){
        this.visible2 = true;
        this.ruleForm.id = record.id;
        this.ruleForm.fastUnit = record.fastUnit;
        this.ruleForm.fastUser = record.fastUser;
        this.ruleForm.fastUserTel = record.fastUserTel;
      },
      downLoadPdf(record){
        this.$refs.modalPdf.edit(record);
      },
      handleSubmit(){
        let url = "/srm/stkIoBill/uploadAttachment";
        let that = this;
        that.$confirm({
          content: `是否确认提交?`,
          onOk: () => {
            putAction(url,that.ruleForm).then(res => {
              if(res.success){
                that.$message.success("提交成功");
                that.handleClose();
              }else{
                that.$message.error("提交失败");
              }
            })
          }
        })
      },
      handleClose(){
        this.visible1 = false;
      },
      uploadFile(record){
        this.ruleForm = {};
        this.visible1 = true;

        this.ruleForm =  JSON.parse(JSON.stringify(record));
      },
      initDictConfig(){
      },
      getSuperFieldList(){
        let fieldList=[];
        fieldList.push({type:'string',value:'invoiceId',text:'开票ID'})
        fieldList.push({type:'string',value:'applyId',text:'付款申请单号'})
        fieldList.push({type:'number',value:'invoiceAmount',text:'开票金额（未税）'})
        fieldList.push({type:'number',value:'invoiceAmountTax',text:'开票金额（含税）'})
        fieldList.push({type:'number',value:'invoiceAmountLocal',text:'开票金额（未税）'})
        fieldList.push({type:'number',value:'invoiceAmountTaxLocal',text:'开票金额（含税）'})
        fieldList.push({type:'string',value:'delFlag',text:'删除标志'})
        this.superFieldList = fieldList
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>