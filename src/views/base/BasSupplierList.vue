<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="名称">
              <a-input placeholder="请输入名称" v-model="queryParam.name"></a-input>
            </a-form-item>
          </a-col>
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="简称">
              <a-input placeholder="请输入简称" v-model="queryParam.shortName"></a-input>
            </a-form-item>
          </a-col>
          <template v-if="toggleSearchStatus">
            <a-col :xl="6" :lg="7" :md="8" :sm="24">
              <a-form-item label="供应商分类">
                <a-input placeholder="请输入供应商分类" v-model="queryParam.supplierCategory"></a-input>
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
      <a-button type="primary" icon="download" @click="handleExportXls('供应商基本信息')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
      <!-- 高级查询区域 -->
<!--      <j-super-query :fieldList="superFieldList" ref="superQueryModal" @handleSuperQuery="handleSuperQuery"></j-super-query>
     -->
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
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

        <span slot="isEnabled" slot-scope="text, record">
          <div v-if="text=='0' ">
            <Tag color="#87d068">正常</Tag>
          </div>
        <div v-if="text=='1' ">
            <Tag color="#108ee9">冻结</Tag>
          </div>
        <div v-if="text=='2' ">
            <Tag color="#f50">拉黑</Tag>
          </div>
        </span>
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
          <a @click="handleEdit(record)">编辑</a>

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
                 <a-menu-item>
                <a-popconfirm title="确定拉黑吗?" @confirm="() => handleDelete(record.id)">
                  <a>拉黑</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
        </span>

      </a-table>
    </div>

    <bas-supplier-modal ref="modalForm" @ok="modalFormOk"/>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import BasSupplierModal from './modules/BasSupplierModal'
  import {filterMultiDictText} from '@/components/dict/JDictSelectUtil'
  import '@/assets/less/TableExpand.less'
  import {isNotNullOrEmpty, isNullOrEmpty} from '@/utils/util'

  export default {
    name: "BasSupplierList",
    mixins:[JeecgListMixin],
    components: {
      BasSupplierModal
    },
    data () {
      return {
        description: '供应商基本信息管理页面',
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
            title:'名称',
            align:"center",
            dataIndex: 'name'
          },
          {
            title:'简称',
            align:"center",
            dataIndex: 'shortName'
          },
      /*    {
            title:'等级',
            align:"center",
            dataIndex: 'supplierLevel'
          },
          {
            title:'纳税规模',
            align:"center",
            dataIndex: 'taxScale'
          },*/
          {
            title:'供应商分类',
            align:"center",
            dataIndex: 'supplierCategory'
          },
          {
            title:'法人代表',
            align:"center",
            dataIndex: 'corporate'
          },
          {
            title:'法人电话',
            align:"center",
            dataIndex: 'corporatePhone'
          },
    /*      {
            title:'供应商地址',
            align:"center",
            dataIndex: 'address'
          },
          {
            title:'开票信息联系电话',
            align:"center",
            dataIndex: 'phoneInvoice'
          },
          {
            title:'收件信息联系电话',
            align:"center",
            dataIndex: 'phoneReceipt'
          },
          {
            title:'附件',
            align:"center",
            dataIndex: 'attachment',
            scopedSlots: {customRender: 'fileSlot'}
          },*/

          {
            title:'状态',
            align:"center",
            dataIndex: 'isEnabled',
            scopedSlots: {customRender: 'isEnabled'},
          },
     /*     {
            title:'供应商性质',
            align:"center",
            dataIndex: 'supplierType_dictText'
          },*/
          {
            title:'系统账号',
            align:"center",
            dataIndex: 'sysAccount'
          },
/*          {
            title:'系统账号初始密码',
            align:"center",
            dataIndex: 'sysPwd'
          },*/
          {
            title:'代理等级',
            align:"center",
            dataIndex: 'level_dictText'
          },
          {
            title:'logo',
            align:"center",
            dataIndex: 'logoUrl',
            scopedSlots: {customRender: 'imgSlot'}
          },
          {
            title:'营业执照',
            align:"center",
            dataIndex: 'supplierAttachment',
            scopedSlots: {customRender: 'imgSlot'}
          },
 /*         {
            title:'授权人身份证号',
            align:"center",
            dataIndex: 'userCardNum'
          },*/
          {
            title:'备注',
            align:"center",
            dataIndex: 'remark'
          },
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
          list: "/srm/basSupplier/list",
          delete: "/srm/basSupplier/delete",
          deleteBatch: "/srm/basSupplier/deleteBatch",
          exportXlsUrl: "/srm/basSupplier/exportXls",
          importExcelUrl: "srm/basSupplier/importExcel",

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
      }
    },
    methods: {
      initDictConfig(){
      },
      getSuperFieldList(){
        let fieldList=[];
        fieldList.push({type:'string',value:'name',text:'名称',dictCode:''})
        fieldList.push({type:'string',value:'shortName',text:'简称',dictCode:''})
        fieldList.push({type:'string',value:'supplierLevel',text:'等级',dictCode:''})
        fieldList.push({type:'string',value:'taxScale',text:'纳税规模',dictCode:''})
        fieldList.push({type:'string',value:'supplierCategory',text:'供应商分类',dictCode:''})
        fieldList.push({type:'string',value:'corporate',text:'法人代表',dictCode:''})
        fieldList.push({type:'string',value:'corporatePhone',text:'法人电话',dictCode:''})
        fieldList.push({type:'string',value:'address',text:'供应商地址',dictCode:''})
        fieldList.push({type:'string',value:'unitInvoice',text:'开票信息单位名称',dictCode:''})
        fieldList.push({type:'string',value:'bankInvoice',text:'开票信息开户行',dictCode:''})
        fieldList.push({type:'string',value:'bankidInvoice',text:'开票信息行号',dictCode:''})
        fieldList.push({type:'string',value:'taxInvoice',text:'开票信息税号',dictCode:''})
        fieldList.push({type:'string',value:'phoneInvoice',text:'开票信息联系电话',dictCode:''})
        fieldList.push({type:'string',value:'accountInvoice',text:'开票信息账号',dictCode:''})
        fieldList.push({type:'string',value:'addressInvoice',text:'开票地址',dictCode:''})
        fieldList.push({type:'string',value:'phoneReceipt',text:'收件信息联系电话',dictCode:''})
        fieldList.push({type:'string',value:'attachment',text:'附件',dictCode:''})
        fieldList.push({type:'string',value:'remark',text:'备注',dictCode:''})
        fieldList.push({type:'switch',value:'isEnabled',text:'是否启用'})
        fieldList.push({type:'string',value:'supplierType',text:'供应商性质',dictCode:'supplier_type'})
        fieldList.push({type:'string',value:'sysAccount',text:'系统账号',dictCode:''})
        fieldList.push({type:'string',value:'sysPwd',text:'系统账号初始密码',dictCode:''})
        fieldList.push({type:'string',value:'level',text:'代理等级',dictCode:''})
        fieldList.push({type:'string',value:'logoUrl',text:'logo',dictCode:''})
        fieldList.push({type:'string',value:'supplierAttachment',text:'营业执照',dictCode:''})
        fieldList.push({type:'string',value:'userCardNum',text:'授权人身份证号',dictCode:''})
        this.superFieldList = fieldList
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>