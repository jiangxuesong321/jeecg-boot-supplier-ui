<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="收款申请编号">
              <a-input placeholder="请输入收款申请编号" v-model="queryParam.applyCode"></a-input>
            </a-form-item>
          </a-col>
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="供应商名称">
              <a-input placeholder="请输入供应商名称" v-model="queryParam.supplierName"></a-input>
            </a-form-item>
          </a-col>


<!--          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="审核状态">
              <a-input placeholder="请选择审核状态" v-model="queryParam.applyStatus"></a-input>
            </a-form-item>
          </a-col>-->

          <template v-if="toggleSearchStatus">
            <a-col :xl="6" :lg="7" :md="8" :sm="24">
              <a-form-item label="项目名称">
                <a-input placeholder="请输入项目名称" v-model="queryParam.projectName"></a-input>
              </a-form-item>
            </a-col>
            <a-col :xl="6" :lg="7" :md="8" :sm="24">
              <a-form-item label="合同名称">
                <a-input placeholder="请输入合同名称" v-model="queryParam.contractName"></a-input>
              </a-form-item>
            </a-col>
          </template>
                    <a-col :xl="6" :lg="7" :md="8" :sm="24">
                      <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
                        <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
                        <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
                        <a @click="handleToggleSearch" style="margin-left: 8px">
                          {{ toggleSearchStatus ? '收起' : '展开' }}
                          <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
                        </a>
                      </span>
                    </a-col>
        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('收款申请')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
<!--      <a-button @click="handleAdd" type="primary" icon="plus">创建收款计划</a-button>-->
      <!-- 高级查询区域 -->
<!--      <j-super-query :fieldList="superFieldList" ref="superQueryModal" @handleSuperQuery="handleSuperQuery"></j-super-query>
     -->
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px" type='primary'> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>
    </div>

    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>

      <a-table
        ref="table"
        size="middle"
        bordered
        rowKey="id"
        class="j-table-force-nowrap"
        :scroll="{x:true}"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        @change="handleTableChange">

        <template slot="htmlSlot" slot-scope="text">
          <div v-html="text"></div>
        </template>
        <template slot="imgSlot" slot-scope="text,record">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无图片</span>
          <img v-else :src="getImgView(text)" :preview="record.id" height="25px" alt="" style="max-width:80px;font-size: 12px;font-style: italic;"/>
        </template>
        <template slot="fileSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无文件</span>
          <a-button
            v-else
            :ghost="true"
            type="primary"
            icon="download"
            size="small"
            @click="downloadFile(text)">
            下载
          </a-button>
        </template>

        <span slot="action" slot-scope="text, record">
            <a @click="handleDetail(record)">详情</a>
<!--          <a @click="handleEdit(record)">编辑</a>

          <a-divider type="vertical" />
          <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a @click="handleDetail(record)">详情</a>
              </a-menu-item>
              <a-menu-item>
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a>删除</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>-->
        </span>

      </a-table>
    </div>

    <pur-pay-apply-modal ref="modalForm" @ok="modalFormOk"/>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import PurPayApplyModal from './modules/PurPayApplyModal'
  import '@/assets/less/TableExpand.less'
  import { filterObj } from '@/utils/util';
  import { deleteAction, getAction,downFile,getFileAccessHttpUrl } from '@/api/manage'
  export default {
    name: "PurPayList",
    mixins:[JeecgListMixin],
    components: {
      PurPayApplyModal
    },
    data () {
      return {
        description: '收款申请管理页面',
        // 表头
        columns: [
          {
            title: '#',
            dataIndex: '',
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
          },
          {
            title:'收款申请单号',
            align:"center",
            dataIndex: 'applyCode'
          },
          {
            title:'供应商名称',
            align:"center",
            dataIndex: 'suppName'
          },
          {
            title:'项目名称',
            align:"center",
            dataIndex: 'projectName'
          },
          {
            title:'合同名称',
            align:"center",
            dataIndex: 'contractName'
          },
          {
            title:'收款金额本币',
            align:"center",
            dataIndex: 'payAmount'
          },
          {
            title:'收款金额原币',
            align:"center",
            dataIndex: 'payAmountOther'
          },
          {
            title:'币种',
            align:"center",
            dataIndex: 'currency'
          },
  /*        {
            title:'税率',
            align:"center",
            dataIndex: 'taxRate'
          },*/
          {
            title:'收款方式',
            align:"center",
            dataIndex: 'payMethod_dictText'
          },
          {
            title:'收款类型',
            align:"center",
            dataIndex: 'payType_dictText'
          },
          {
            title:'收款比例(%)',
            align:"center",
            dataIndex: 'payRate'
          },

  /*        {
            title:'收款开户行',
            align:"center",
            dataIndex: 'receivingBank'
          },
          {
            title:'收款账号',
            align:"center",
            dataIndex: 'receivingNumber'
          },*/
          {
            title:'申请理由',
            align:"center",
            dataIndex: 'applyReason'
          },
          {
            title:'申请状态',
            align:"center",
            dataIndex: 'applyStatus_dictText'
          },
          {
            title:'申请时间',
            align:"center",
            dataIndex: 'applyTime',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'审核时间',
            align:"center",
            dataIndex: 'auditorDate',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },


          {
            title:'备注',
            align:"center",
            dataIndex: 'comment'
          },
  /*        {
            title:'应收款日期',
            align:"center",
            dataIndex: 'dueDate',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'供应商id',
            align:"center",
            dataIndex: 'suppId'
          },
         {
            title:'驳回理由',
            align:"center",
            dataIndex: 'returnReason'
          },
          {
            title:'是否需要发票',
            align:"center",
            dataIndex: 'isNeedInvoice'
          },

          {
            title:'排序',
            align:"center",
            dataIndex: 'sort'
          },

          {
            title:'收款状态【0：未收款；1：部分收款；2：已完成收款；3已关闭】',
            align:"center",
            dataIndex: 'payStatus'
          },
          {
            title:'审核人',
            align:"center",
            dataIndex: 'auditor'
          },
          {
            title:'审核日期',
            align:"center",
            dataIndex: 'auditorDate',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'发票金额',
            align:"center",
            dataIndex: 'invoiceAmount'
          },
          {
            title:'发票号',
            align:"center",
            dataIndex: 'invoiceNumber'
          },
          {
            title:'发票时间',
            align:"center",
            dataIndex: 'invoiceDate',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'发票代码',
            align:"center",
            dataIndex: 'invoiceCode'
          },
          {
            title:'审批人ID',
            align:"center",
            dataIndex: 'approvalUserId'
          },
          {
            title:'审批人',
            align:"center",
            dataIndex: 'approvalUserName'
          },
          {
            title:'审批时间',
            align:"center",
            dataIndex: 'approvalTime',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },

          {
            title:'贸易商自己的收款方式',
            align:"center",
            dataIndex: 'traderPayment'
          },
          {
            title:'收款追加类型',
            align:"center",
            dataIndex: 'addToType'
          },
          {
            title:'流程实例ID',
            align:"center",
            dataIndex: 'processInstanceId'
          },
         {
            title:'审批状态(0:待审批,1:审批通过)',
            align:"center",
            dataIndex: 'checkStatus'
          },
          {
            title:'受理审批',
            align:"center",
            dataIndex: 'acceptComment'
          },
          {
            title:'是否整单(1：整单支付 2：部分支付)',
            align:"center",
            dataIndex: 'isWholeSheet'
          },*/

/*          {
            title:'收款申请金额',
            align:"center",
            dataIndex: 'applyPayAmount'
          },
          {
            title:'收款申请来源（00：非月结，10月结）',
            align:"center",
            dataIndex: 'applySource'
          },
          {
            title:'申请附件地址',
            align:"center",
            dataIndex: 'attachment'
          },
          {
            title:'实际收款金额',
            align:"center",
            dataIndex: 'actPayAmount'
          },*/
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            fixed:"right",
            width:147,
            scopedSlots: { customRender: 'action' },
          }
        ],
        url: {
          list: "/srm/purPayApply/list",
          delete: "/srm/purPayApply/delete",
          deleteBatch: "/srm/purPayApply/deleteBatch",
          exportXlsUrl: "/srm/purPayApply/exportXls",
          importExcelUrl: "srm/purPayApply/importExcel",

        },
        dictOptions:{},
        superFieldList:[],
        queryParam:{
          applyStatus:'30'
        },
      }
    },
    created() {
      this.getSuperFieldList();
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },
    methods: {
      getQueryParams() {
        //获取查询条件
        let sqp = {}
        if(this.superQueryParams){
          sqp['superQueryParams']=encodeURI(this.superQueryParams)
          sqp['superQueryMatchType'] = this.superQueryMatchType
        }
        var param = Object.assign(sqp, this.queryParam, this.isorter ,this.filters);
        param.field = this.getQueryField();
        param.pageNo = this.ipagination.current;
        param.pageSize = this.ipagination.pageSize;
        return filterObj(param);
      },
      loadData(arg) {
        if(!this.url.list){
          this.$message.error("请设置url.list属性!")
          return
        }
        //加载数据 若传入参数1则加载第一页的内容
        if (arg === 1) {
          this.ipagination.current = 1;
        }
        var params = this.getQueryParams();//查询条件
        this.loading = true;
        getAction(this.url.list, params).then((res) => {
          if (res.success) {
            //update-begin---author:zhangyafei    Date:20201118  for：适配不分页的数据列表------------
            this.dataSource = res.result.records||res.result;
            if(res.result.total)
            {
              this.ipagination.total = res.result.total;
            }else{
              this.ipagination.total = 0;
            }
            //update-end---author:zhangyafei    Date:20201118  for：适配不分页的数据列表------------
          }else{
            this.$message.warning(res.message)
          }
        }).finally(() => {
          this.loading = false
        })
      },
      initDictConfig(){
      },
      getSuperFieldList(){
        let fieldList=[];
        fieldList.push({type:'string',value:'applyCode',text:'收款申请单号',dictCode:''})
        fieldList.push({type:'BigDecimal',value:'payAmount',text:'收款金额',dictCode:''})
        fieldList.push({type:'BigDecimal',value:'taxRate',text:'税率',dictCode:''})
        fieldList.push({type:'string',value:'payMethod',text:'收款方式',dictCode:''})
        fieldList.push({type:'string',value:'currency',text:'币种',dictCode:''})
        fieldList.push({type:'string',value:'applyReason',text:'申请理由',dictCode:''})
        fieldList.push({type:'string',value:'applyStatus',text:'申请状态：00：待审核10：已驳回20:已受理,30:部分收款,40:收款完成',dictCode:''})
        fieldList.push({type:'date',value:'applyTime',text:'申请时间'})
        fieldList.push({type:'string',value:'receivingBank',text:'收款开户行',dictCode:''})
        fieldList.push({type:'string',value:'receivingNumber',text:'收款账号',dictCode:''})
        fieldList.push({type:'date',value:'dueDate',text:'应收款日期'})
        fieldList.push({type:'string',value:'suppId',text:'供应商id',dictCode:''})
        fieldList.push({type:'string',value:'returnReason',text:'驳回理由',dictCode:''})
        fieldList.push({type:'string',value:'isNeedInvoice',text:'是否需要发票',dictCode:''})
        fieldList.push({type:'string',value:'comment',text:'备注',dictCode:''})
        fieldList.push({type:'int',value:'sort',text:'排序',dictCode:''})
        fieldList.push({type:'string',value:'tenantId',text:'租户ID',dictCode:''})
        fieldList.push({type:'string',value:'delFlag',text:'删除标志位',dictCode:''})
        fieldList.push({type:'string',value:'createUser',text:'创建人',dictCode:''})
        fieldList.push({type:'string',value:'updateUser',text:'修改人',dictCode:''})
        fieldList.push({type:'string',value:'payStatus',text:'收款状态【0：未收款；1：部分收款；2：已完成收款；3已关闭】',dictCode:''})
        fieldList.push({type:'string',value:'auditor',text:'审核人',dictCode:''})
        fieldList.push({type:'date',value:'auditorDate',text:'审核日期'})
        fieldList.push({type:'BigDecimal',value:'invoiceAmount',text:'发票金额',dictCode:''})
        fieldList.push({type:'string',value:'invoiceNumber',text:'发票号',dictCode:''})
        fieldList.push({type:'date',value:'invoiceDate',text:'发票时间'})
        fieldList.push({type:'string',value:'invoiceCode',text:'发票代码',dictCode:''})
        fieldList.push({type:'string',value:'approvalUserId',text:'审批人ID',dictCode:''})
        fieldList.push({type:'string',value:'approvalUserName',text:'审批人',dictCode:''})
        fieldList.push({type:'date',value:'approvalTime',text:'审批时间'})
        fieldList.push({type:'string',value:'payType',text:'收款类型：00：预收款 10：月结款',dictCode:''})
        fieldList.push({type:'string',value:'traderPayment',text:'贸易商自己的收款方式',dictCode:''})
        fieldList.push({type:'string',value:'addToType',text:'收款追加类型',dictCode:''})
        fieldList.push({type:'string',value:'processInstanceId',text:'流程实例ID',dictCode:''})
        fieldList.push({type:'string',value:'checkStatus',text:'审批状态(0:待审批,1:审批通过)',dictCode:''})
        fieldList.push({type:'string',value:'acceptComment',text:'受理审批',dictCode:''})
        fieldList.push({type:'string',value:'isWholeSheet',text:'是否整单(1：整单支付 2：部分支付)',dictCode:''})
        fieldList.push({type:'BigDecimal',value:'payRate',text:'收款比例',dictCode:''})
        fieldList.push({type:'string',value:'suppName',text:'供应商名称',dictCode:''})
        fieldList.push({type:'BigDecimal',value:'applyPayAmount',text:'收款申请金额',dictCode:''})
        fieldList.push({type:'string',value:'applySource',text:'收款申请来源（00：非月结，10月结）',dictCode:''})
        fieldList.push({type:'Text',value:'attachment',text:'申请附件地址',dictCode:''})
        fieldList.push({type:'BigDecimal',value:'actPayAmount',text:'实际收款金额',dictCode:''})
        this.superFieldList = fieldList
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>