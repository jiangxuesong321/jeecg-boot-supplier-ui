<template>
  <a-card :bordered='false'>
    <!-- 查询区域 -->
    <div class='table-page-search-wrapper'>
      <a-form layout='inline' @keyup.enter.native='searchQuery'>
        <a-row :gutter='24'>
          <a-col :span='6'>
            <a-form-item label='询价单号'>
              <a-input placeholder='请输入询价单号' v-model='queryParam.inquiryCode'></a-input>
            </a-form-item>
          </a-col>
          <a-col :span='6'>
            <a-form-item label='设备名称'>
              <a-input placeholder='请输入询价设备' v-model='queryParam.prodName'></a-input>
            </a-form-item>
          </a-col>
          <a-col :span='6'>
            <a-form-item label='设备编码'>
              <a-input placeholder='请输入询价单号' v-model='queryParam.prodCode'></a-input>
            </a-form-item>
          </a-col>
          <a-col :span='6'>
						<span style='float: left;overflow: hidden;' class='table-page-search-submitButtons'>
							<a-button type='primary' @click='searchQuery' icon='search'>查询</a-button>
							<a-button type='primary' @click='searchReset' icon='reload' style='margin-left: 8px'>重置</a-button>
						</span>
          </a-col>
        </a-row>
      </a-form>
    </div>

    <!-- 操作按钮区域 -->
    <div class='table-operator'>
    </div>

    <div>
      <a-table ref='table'
               size='middle'
               rowKey='id'
               :scroll='{x:true, y:500}'
               :columns='columns'
               :dataSource='dataSource'
               :pagination='ipagination'
               :loading='loading'
               :customRow='customRow'
               @change='handleTableChange'>


				<span slot='action' slot-scope='text, record'>
          <span v-if='record.inquiryStatus == "4" || record.inquiryStatus == "3"'>
            <a @click='handleDetail(record)'>查看报价</a>
          </span>
          <span v-else>
            <a @click='handleEdit(record)' v-if="record.status == '0' && record.isShow == '1'">报价</a>
            <a-divider type="vertical" v-if="record.status == '0' && record.isShow == '1'"/>

            <a @click='handleEditTwo(record)' v-if="record.status == '1' && record.isShow == '1'">更新报价</a>
            <a-divider type="vertical" v-if="record.status == '1' && record.isShow == '1'"/>

            <a @click='handleBargain(record)' v-if="record.status == '2'">议价</a>
            <a-divider type="vertical" v-if="record.status == '2'"/>

            <a @click='handleDetail(record)' v-if="record.status == '1' || record.status == '3'">查看报价</a>
          </span>
				</span>

      </a-table>
    </div>
    <inquiry-list-modal ref='modalForm' @ok='modalFormOk' />
  </a-card>
</template>

<script>
import {
  JeecgListMixin
} from '@/mixins/JeecgListMixin'
import InquiryListModal from './modules/InquiryListModal'
import '@/assets/less/TableExpand.less'
import { getAction } from '@api/manage'

export default {
  name: 'InquiryListList',
  mixins: [JeecgListMixin],
  components: {
    InquiryListModal
  },
  data() {
    return {
      description: '询价单主表管理页面',
      queryParam: {
        inquiryCode: '',
        prodCode: '',
        prodName: '',
        status: '0'
      },
      // 表头
      columns: [
        {
          title: '序号',
          dataIndex: '',
          key: 'rowIndex',
          width: 60,
          align: 'center',
          customRender: function(t, r, index) {
            return parseInt(index) + 1
          }
        },
        {
          title: '询价编号',
          align: 'center',
          dataIndex: 'inquiryCode',
          width: 140,
        },
        // {
        //   title: '设备编号',
        //   align: 'center',
        //   dataIndex: 'prodCode',
        //   width: 120,
        // },
        {
          title: '询价设备',
          align: 'center',
          dataIndex: 'prodName',
          width: 160,
        },
        // {
        //   title: '询价设备描述',
        //   align: 'center',
        //   dataIndex: 'speType',
        //   width: 120,
        // },
        {
          title: '数量',
          align: 'center',
          dataIndex: 'qty',
          width: 120,
        },
        {
          title: '单位',
          align: 'center',
          dataIndex: 'unitId_dictText',
          width: 120,
        },
        {
          title: '询价时间',
          align: 'center',
          dataIndex: 'createTime',
          width: 120,
        },
        {
          title: '报价截止日期',
          align: 'center',
          dataIndex: 'quotationDeadline',
          width: 120,
        },
        {
          title: '交期',
          align: 'center',
          dataIndex: 'leadTime',
          customRender: function(text) {
            return !text ? '' : (text.length > 10 ? text.substr(0, 10) : text)
          },
          width: 120,
        },
        {
          title: '询价状态',
          align: 'center',
          dataIndex: 'status',
          customRender: function(text,record) {
            if(record.inquiryStatus == '4'){
              return '作废';
            }else if(record.inquiryStatus == '3'){
              return '暂停';
            }else{
              if(text == '0'){
                return '询价中';
              }else if(text == '1'){
                return '已报价';
              }else if(text == '2'){
                return '议价中';
              }else if(text == '3'){
                return '议价已回复';
              }else if(text == '4'){
                return '已结束';
              }
            }

          },
          width: 120,
        },
        {
          title: '采购说明',
          align: 'center',
          dataIndex: 'remark',
          width: 200,
        },
        {
          title: '操作',
          dataIndex: 'action',
          align: 'center',
          width: 147,
          scopedSlots: {
            customRender: 'action'
          }
        }
      ],
      url: {
        list: '/srm/inquiryRecordList/list',
        delete: '/srm/inquiryList/delete',
        deleteBatch: '/srm/inquiryList/deleteBatch',
        exportXlsUrl: '/srm/inquiryList/exportXls',
        importExcelUrl: 'srm/inquiryList/importExcel'

      },
      dictOptions: {},
      superFieldList: []
    }
  },
  created() {

  },
  computed: {
    importExcelUrl: function() {
      return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`
    }
  },
  methods: {
    handleBargain(record){
      this.$refs.modalForm.bargain(record);
      this.$refs.modalForm.title="议价";
      this.$refs.modalForm.disableSubmit = false;
    },
    handleEditTwo(record){
      this.$refs.modalForm.editTwo(record);
      this.$refs.modalForm.title="详情";
      this.$refs.modalForm.disableSubmit = false;
    },
    handleDetail:function(record){
      this.$refs.modalForm.detail(record);
      this.$refs.modalForm.title="详情";
      this.$refs.modalForm.disableSubmit = true;
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
      params.status = '0,1'
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
    customRow(record, index) {
      return {
        // 这个style就是我自定义的属性，也就是官方文档中的props
        style: {
          // 行背景色
          'background-color': index % 2 !== 0 ? '#f0f0f0' : '#fefefe',
          // 字体类型
          'font-family': 'Microsoft YaHei'
        }
      }
    },


  }
}
</script>
<style scoped>
@import '~@assets/less/common.less';
</style>
<style lang='less' scoped>
.ant-row.ant-form-item {
  margin-bottom: 12px;
}
</style>
