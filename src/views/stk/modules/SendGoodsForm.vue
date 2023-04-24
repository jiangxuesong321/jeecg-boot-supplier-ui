<template>
  <div>
    <j-form-container>
        <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
          <a-row>
            <a-col :span="8">
              <a-form-model-item label="发货单号" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
                <a-input v-model="model.billNo" placeholder="发货单号自动生成"  disabled></a-input>
              </a-form-model-item>
            </a-col>

            <a-col :span="8">
              <a-form-model-item label="合同名称" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractId">
                <j-select-contract ref='contract' v-model="model.contractId" :multi="false" @change="backUser" :disabled="formDisabled"></j-select-contract>
              </a-form-model-item>
            </a-col>

            <a-col :span="8">
              <a-form-model-item label="合同编码" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="contractNumber">
                <a-input v-model="model.contractNumber" placeholder="合同编码"  disabled></a-input>
              </a-form-model-item>
            </a-col>
          </a-row>
          <a-row>
            <a-col :span="8">
              <a-form-model-item label="发货人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="sendUser">
                <a-input v-model="model.sendUser" placeholder="请输入发货人" style="width: 100%" :disabled="formDisabled"/>
              </a-form-model-item>
            </a-col>

            <a-col :span="8">
              <a-form-model-item label="发货人联系方式" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="sendUserTel">
                <a-input v-model="model.sendUserTel" placeholder="请输入发货人联系方式" style="width: 100%" :disabled="formDisabled"/>
              </a-form-model-item>
            </a-col>

            <a-col :span="8">
              <a-form-model-item label="收货人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="receiveUser">
                <a-input v-model="model.receiveUser" placeholder="请输入收货人" style="width: 100%" :disabled="formDisabled"/>
              </a-form-model-item>
            </a-col>
          </a-row>
          <a-row>
            <a-col :span="8">
              <a-form-model-item label="收货人联系方式" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="receiveUserTel">
                <a-input v-model="model.receiveUserTel" placeholder="请输入收货人联系方式" style="width: 100%" :disabled="formDisabled"/>
              </a-form-model-item>
            </a-col>

            <a-col :span="8">
              <a-form-model-item label="承运单位" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >
                <a-input v-model="model.fastUnit" placeholder="请输入承运单位" style="width: 100%" :disabled="formDisabled"/>
              </a-form-model-item>
            </a-col>

            <a-col :span="8">
              <a-form-model-item label="承运联系人" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
                <a-input v-model="model.fastUser" placeholder="请输入承运联系人" style="width: 100%" :disabled="formDisabled"/>
              </a-form-model-item>
            </a-col>
          </a-row>
          <a-row>
            <a-col :span="8">
              <a-form-model-item label="承运人联系方式" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="receiveUserTel">
                <a-input v-model="model.fastUserTel" placeholder="请输入承运人联系方式" style="width: 100%" :disabled="formDisabled"/>
              </a-form-model-item>
            </a-col>

<!--            <a-col :span="8">-->
<!--              <a-form-model-item label="发货时间" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="sendTime">-->
<!--                <a-date-picker v-model="model.sendTime" placeholder="发货时间" style="width: 100%;" :disabled="formDisabled"/>-->
<!--              </a-form-model-item>-->
<!--            </a-col>-->
          </a-row>

          <a-divider orientation="left" style="color: #00A0E9">
            设备明细
          </a-divider>
          <a-button type='primary' @click='openDrawer' icon='plus' style='float: right;z-index: 999' :disabled="formDisabled">选择合同设备</a-button>
          <a-table
            ref="table"
            size="small"
            rowKey="id"
            bordered
            :scroll="{x:1200}"
            :columns="columns"
            :dataSource="dataSource"
            :pagination="false">
            <template slot='sendTime' slot-scope='text,record'>
              <a-date-picker v-model="record.sendTime" placeholder="发货时间" style="width: 100%;" :disabled="formDisabled"/>
            </template>
            <template slot='packNo' slot-scope='text,record'>
              <a-input v-model='record.packNo' :disabled="formDisabled"></a-input>
            </template>
            <template slot='seqNo' slot-scope='text,record'>
              <a-input v-model='record.seqNo' :disabled="formDisabled"></a-input>
            </template>
            <template slot='socialNo' slot-scope='text,record'>
              <a-input v-model='record.socialNo' :disabled="formDisabled"></a-input>
            </template>
            <span slot="action" slot-scope="text, record,index">
              <a @click='handleDelete(index)' :disabled="formDisabled">删除</a>
					  </span>
          </a-table>
          <a-divider orientation="left" style="color: #00A0E9">
            备注
          </a-divider>
          <a-row>
            <a-col :span="8">
              <a-form-model-item label="备注" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="sendTime">
                <a-input v-model="model.sendRemark" placeholder="备注" :disabled="formDisabled" type='textarea'/>
              </a-form-model-item>
            </a-col>
          </a-row>
          <a-divider orientation="left" style="color: #00A0E9" v-if='model.attachment != null && model.attachment != "" && model.attachment != undefined'>
            签收单
          </a-divider>
          <a-row v-if='model.attachment != null && model.attachment != "" && model.attachment != undefined'>
            <a-col :span="8">
              <a-form-model-item label="签收单" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="attachment">
                <j-upload v-model="model.attachment" :trigger-change="true" disabled></j-upload>
              </a-form-model-item>
            </a-col>
          </a-row>
        </a-form-model>
      <contract-object-modal ref='modal' :contractId='model.contractId' @ok='back' ptype='stk' :ids='ids'></contract-object-modal>
    </j-form-container>
  </div>
</template>

<script>

  import { httpAction, getAction } from '@/api/manage'
  import { iegAmount, isNullOrEmpty, preciseNum, validateDuplicateValue } from '@/utils/util'
  import { billListMixin } from '../../mixins/billListMixin'
  import { billModalMixin } from '../../mixins/billModalMixin'
  import JSelectContract from '@comp/jeecgbiz/JSelectContract'
  import ContractObjectModal from '@views/pay/modules/ContractObjectModal'
  import html2canvas from 'html2canvas'
  import JsPDF from 'jspdf'
  export default {
    name: 'SendGoodsForm',
    mixins: [billListMixin, billModalMixin],
    components: {
      JSelectContract,
      ContractObjectModal
    },
    props: {
      //表单禁用
      disabled: {
        type: Boolean,
        default: false,
        required: false
      }
    },
    data () {
      return {
        ids:'',
        columns: [{
          title: '序号',
          dataIndex: '',
          key: 'rowIndex',
          width: 60,
          align: "center",
          customRender: function(t, r, index) {
            return parseInt(index) + 1;
          }
          },
          {
            title: '设备名称',
            align: "center",
            dataIndex: 'prodName',
            width: 120,
          },
          {
            title: '规格型号',
            align: "center",
            dataIndex: 'prodSpecType',
            ellipsis:true,
            width: 120,
          },
          {
            title: '发货数量',
            align: "center",
            dataIndex: 'qty',
            width: 120,
          },
          {
            title: '设备序号',
            align: "center",
            dataIndex: 'sort',
            width: 120,
          },
          {
            title: '序列号',
            align: "center",
            dataIndex: 'seqNo',
            width: 120,
            scopedSlots: {customRender: 'seqNo'}
          },
          {
            title: '信用证编码',
            align: "center",
            dataIndex: 'socialNo',
            width: 120,
            scopedSlots: {customRender: 'socialNo'}
          },
          {
            title: '箱单编号',
            align: "center",
            dataIndex: 'packNo',
            width: 120,
            scopedSlots: {customRender: 'packNo'}
          },
          {
            title: '发货日期',
            align: "center",
            dataIndex: 'sendTime',
            width: 120,
            scopedSlots: {customRender: 'sendTime'}
          },
          {
            title: '操作',
            dataIndex: 'action',
            align: "center",
            width: 100,
            scopedSlots: {customRender: 'action'}
          }
        ],
        dataSource:[],
        oldList:[],
        model:{
         },
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        confirmLoading: false,
        validatorRules: {
          contractId: [
            { required: true,  message: '请选择合同', trigger: 'change' }
          ],
          sendUser: [
            { required: true,  message: '请输入发货人', trigger: 'blur' }
          ],
          sendUserTel: [
            { required: true,  message: '请输入发货人联系方式', trigger: 'blur' }
          ],
        },
        url: {
          add: "/srm/stkIoBill/add",
          edit: "/srm/stkIoBill/edit",
          queryById: "/srm/purchaseApplyInvoice/queryById"
        }
      }
    },
    computed: {
      formDisabled(){
        return this.disabled
      },
    },
    created () {
       //备份model原始值
      this.modelDefault = JSON.parse(JSON.stringify(this.model));
    },
    methods: {
      pdfDownload(){
        let _this = this
        let myBox = _this.$refs.orderForm1; //获取ref里面的内容
        html2canvas(myBox, {
          useCORS: true, //是否尝试使用CORS从服务器加载图像
          allowTaint: true,
          dpi: 300, //解决生产图片模糊
          scale: 3, //清晰度--放大倍数
        }).then(function (canvas) {
          let contentWidth = canvas.width
          let contentHeight = canvas.height
          let pageHeight = contentWidth / 592.28 * 841.89 // 一页pdf显示html页面生成的canvas高度;
          let leftHeight = contentHeight //未生成pdf的html页面高度
          let position = 0 //pdf页面偏移
          //a4纸的尺寸[595.28,841.89]，html页面生成的canvas在pdf中图片的宽高
          // let imgWidth = 595.28
          let imgWidth = 560.28  //宽度
          let imgHeight = 592.28 / contentWidth * contentHeight
          let pageData = canvas.toDataURL('image/jpeg', 1.0)
          let PDF = new JsPDF('', 'pt', 'a4')

          // 有两个高度需要区分，一个是html页面的实际高度，和生成pdf的页面高度(841.89)
          //当内容未超过pdf一页显示的范围，无需分页
          if (leftHeight < pageHeight) {
            PDF.addImage(pageData, 'JPEG', 20, 20, imgWidth, imgHeight)
          } else {
            while (leftHeight > 0) {
              PDF.addImage(pageData, 'JPEG', 20, position, imgWidth, imgHeight)
              leftHeight -= pageHeight
              position -= 841.89
              if (leftHeight > 0) {
                PDF.addPage()
              }
            }
          }
          PDF.save('发货单.pdf')//下载标题
        });
      },
      back(rows){
        rows.filter(item => {
          let flag = false;
          this.dataSource.filter(child => {
            if(item.id == child.orderDetailId){
              flag = true;
              return;
            }
          })
          if(!flag){
            item.materialId = item.prodId;
            item.orderDetailId = item.id;
            this.dataSource.push(item);
          }
        })
        this.$message.success("添加成功");
        // setTimeout(() => {
        //   this.setAmount();
        // }, 500)
      },
      handleDelete(index){
        this.dataSource.splice(index,1);
      },
      openDrawer(){
        let that = this;
        let contractId = that.model.contractId;
        if(isNullOrEmpty(contractId)){
          that.$message.error("请选择合同");
          return;
        }
        that.ids = '';
        let ids = [];
        if(that.oldList != null && that.oldList.length > 0){
          that.oldList.filter(item => {
            ids.push(item.orderDetailId);
          })
          that.ids = ids.join(",");
        }
        setTimeout(() => {
          that.$refs.modal.loadData();
        }, 400)

      },
      backUser(ids,rows){
        this.model.contractName = rows[0].contractName;
        this.model.contractNumber = rows[0].contractNumber;
        this.model.projectId = rows[0].projectId;
        this.dataSource = [];
        this.$forceUpdate();
      },
      add () {
        this.edit(this.modelDefault);
      },
      edit (record) {
        let that = this;
        this.model = Object.assign({}, record);
        this.dataSource = [];
        this.oldList = [];
        this.visible = true;
        if(that.model.id){
          this.fetchDetailList(that.model.id);
        }
      },
      fetchDetailList(id){
        let url = "/srm/stkIoBill/queryStkIoBillEntryByMainId";
        getAction(url,{id:id}).then(res => {
          let data = res.result;
          this.dataSource = data;
          this.oldList = JSON.parse(JSON.stringify(data));
        })
      },
      submitForm () {
        const that = this;
        // 触发表单验证
        that.$refs.form.validate(valid => {
          if (valid) {
            let httpurl = '';
            let method = '';
            if(!that.model.id){
              httpurl+=this.url.add;
              method = 'post';
            }else{
              httpurl+=this.url.edit;
               method = 'put';
            }
            let dataSource = that.dataSource;
            if(dataSource == null || dataSource.length == 0){
              that.$message.error("请选择发货明细");
              return;
            }
            for(let i = 0; i < dataSource.length; i++){
              if(isNullOrEmpty(dataSource[i].seqNo)){
                that.$message.error("第" + (i+1) + "行,请输入序列号");
                return;
              }
              if(isNullOrEmpty(dataSource[i].sendTime)){
                that.$message.error("第" + (i+1) + "行,请输入发货时间");
                return;
              }
            }
            that.model.stkIoBillEntryList = dataSource;
            that.$confirm({
              content: `是否确认提交?`,
              onOk: () => {
                httpAction(httpurl,this.model,method).then((res)=>{
                  if(res.success){
                    that.$message.success(res.message);
                    that.$emit('ok');
                  }else{
                    that.$message.warning(res.message);
                  }
                })
              }
            })
          }
        })
      },
    }
  }
</script>