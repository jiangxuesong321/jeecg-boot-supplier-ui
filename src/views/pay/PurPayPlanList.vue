<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="收款月份">
              <a-input placeholder="请输入收款月份" v-model="queryParam.planMonth"></a-input>
            </a-form-item>
          </a-col>
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
      <a-button type="primary" icon="download" @click="handleExportXls('收款计划')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
      <!-- 高级查询区域 -->
      <j-super-query :fieldList="superFieldList" ref="superQueryModal" @handleSuperQuery="handleSuperQuery"></j-super-query>
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
            </a-menu>
          </a-dropdown>
        </span>

      </a-table>
    </div>

    <pur-pay-plan-modal ref="modalForm" @ok="modalFormOk"/>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import PurPayPlanModal from './modules/PurPayPlanModal'
  import '@/assets/less/TableExpand.less'

  export default {
    name: "PurPayPlanList",
    mixins:[JeecgListMixin],
    components: {
      PurPayPlanModal
    },
    data () {
      return {
        description: '收款计划管理页面',
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
            title:'收款月份',
            align:"center",
            dataIndex: 'planMonth'
          },
          {
            title:'应付(本币)',
            align:"center",
            dataIndex: 'payAmountCope'
          },


          {
            title:'收款比例(%)',
            align:"center",
            dataIndex: 'payRate'
          },
          {
            title:'已付(本币)',
            align:"center",
            dataIndex: 'payAmountPaid'
          },
          {
            title:'收款状态',
            align:"center",
            dataIndex: 'payStatus_dictText'
          },
          {
            title:'应付(美元)',
            align:"center",
            dataIndex: 'payAmountUsd'
          },
          {
            title:'应付(日元)',
            align:"center",
            dataIndex: 'payAmountJpy'
          },
          {
            title:'应付(欧元)',
            align:"center",
            dataIndex: 'payAmountEur'
          },
          {
            title:'备注',
            align:"center",
            dataIndex: 'comment'
          },
          {
            title:'创建人',
            align:"center",
            dataIndex: 'createUser'
          },
          {
            title:'修改人',
            align:"center",
            dataIndex: 'updateUser'
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
          list: "/srm/purPayPlan/list",
          delete: "/srm/purPayPlan/delete",
          deleteBatch: "/srm/purPayPlan/deleteBatch",
          exportXlsUrl: "/srm/purPayPlan/exportXls",
          importExcelUrl: "srm/purPayPlan/importExcel",

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
        fieldList.push({type:'string',value:'planMonth',text:'收款月份',dictCode:''})
        fieldList.push({type:'BigDecimal',value:'payAmountCope',text:'应收款金额本币',dictCode:''})
        fieldList.push({type:'string',value:'comment',text:'备注',dictCode:''})
        fieldList.push({type:'string',value:'createUser',text:'创建人',dictCode:''})
        fieldList.push({type:'string',value:'updateUser',text:'修改人',dictCode:''})
        fieldList.push({type:'string',value:'payStatus',text:'收款状态【0：未收款；1：部分收款；2：已完成收款；3已关闭】',dictCode:''})
        fieldList.push({type:'BigDecimal',value:'payRate',text:'收款比例',dictCode:''})
        fieldList.push({type:'BigDecimal',value:'payAmountPaid',text:'实际收款金额本币',dictCode:''})
        fieldList.push({type:'BigDecimal',value:'payAmountUsd',text:'应收款金额美元',dictCode:''})
        fieldList.push({type:'BigDecimal',value:'payAmountJpy',text:'应收款金额日元',dictCode:''})
        fieldList.push({type:'BigDecimal',value:'payAmountEur',text:'应收款金额欧元',dictCode:''})
        this.superFieldList = fieldList
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>