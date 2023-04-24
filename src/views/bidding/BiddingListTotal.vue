<template>
  <a-card :bordered="false">
    <a-tabs v-model='tabKey' @change="callback">
      <a-tab-pane tab="进行中" key="0">
      </a-tab-pane>

      <a-tab-pane tab="投标历史" key="1">
      </a-tab-pane>

      <a-tab-pane tab="中标议价" key="2">
        <bidding-bargin-list ref='bargin'></bidding-bargin-list>
      </a-tab-pane>
    </a-tabs>
    <div v-if='tabKey != "2"'>
      <div class="table-page-search-wrapper">
        <a-form layout="inline" @keyup.enter.native="searchQuery">
          <a-row :gutter="24">
            <a-col :span="6">
              <a-form-item label="招标名称" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
                <a-input placeholder="请输入招标名称" v-model="queryParam.biddingName"></a-input>
              </a-form-item>
            </a-col>
            <a-col :span="6">
              <a-form-item label="招标编码" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
                <a-input placeholder="请输入招标编码" v-model="queryParam.biddingNo"></a-input>
              </a-form-item>
            </a-col>
            <a-col :span="6">
						<span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
							<a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
							<a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置
							</a-button>
						</span>
            </a-col>
          </a-row>
        </a-form>
      </div>
      <div>
        <a-table ref="table" size="middle" rowKey="id" :scroll="{x:true,y:500}"
                 :columns="columns" :dataSource="dataSource" :pagination="ipagination" :loading="loading" :customRow="customRow"
                 @change="handleTableChange">

          <template slot='biddingNo' slot-scope='text,record'>
            <a @click='handleDetail(record)'>{{text}}</a>
          </template>

          <span slot="action" slot-scope="text, record">
					<div v-if="record.status == '0' && record.isAction == '1'">
            <a-popconfirm title="确定接受吗?" @confirm="() => handleAccept(record)">
              <a>接受</a>
            </a-popconfirm>
					  <a-divider type="vertical" />
            <a-popconfirm title="确定放弃吗?" @confirm="() => handleReject(record.id)">
              <a>放弃</a>
            </a-popconfirm>
					</div>
					<div v-if="record.status == '2' || (record.status == '3' && record.isQuotes == '1')">
						<a @click="handleEdit(record)" v-if='tabKey == "0" && record.isAction == "1"'>报价</a>
					</div>
				</span>

        </a-table>
      </div>
      <bidding-main-modal ref="modalForm" @ok="modalFormOk" />
    </div>

  </a-card>
</template>

<script>
import {
  JeecgListMixin
} from '@/mixins/JeecgListMixin'
import BiddingMainModal from './modules/BiddingMainModal'
import '@/assets/less/TableExpand.less'
import {
  billListMixin
} from '../mixins/billListMixin'
import {
  billModalMixin
} from '../mixins/billModalMixin'
import JEllipsis from '@/components/jeecg/JEllipsis'
import { getAction,putAction } from '@api/manage'
import BiddingBarginList from '@views/bidding/BiddingBarginList'
export default {
  name: "BiddingMainList",
  mixins: [JeecgListMixin, billListMixin, billModalMixin],
  components: {
    BiddingMainModal,
    JEllipsis,
    BiddingBarginList
  },
  data() {
    return {
      tabKey:'0',
      description: '招标主表管理页面',
      disableMixinCreated: true,
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
          title: '招标编码',
          dataIndex: 'biddingNo',
          scopedSlots: {
            customRender: 'biddingNo'
          },
          width: 120,
          align: "center",
        },
        {
          title: '招标名称',
          dataIndex: 'biddingName',
          width: 120,
          align: "center",
        },
        {
          title: '招标截至时间',
          dataIndex: 'biddingDeadline',
          customRender: function(text) {
            return !text ? "" : (text.length > 10 ? text.substr(0, 10) : text)
          },
          width: 120,
          align: "center",
        },

        {
          title: '招标类型',
          dataIndex: 'biddingType_dictText',
          width: 120,
          align: "center",
        },

        {
          title: '招标状态',
          dataIndex: 'status',
          width: 120,
          customRender: function(text) {
            if(text == '0'){
              return "待受理"
            }else if(text == '1'){
              return "已放弃";
            }else if(text == "2"){
              return "已受理";
            }else if(text == "3"){
              return "已报价";
            }else if(text == "01"){
              return "已开标";
            }else if(text == "04"){
              return "已废标";
            }else if(text == '4'){
              return "已结束";
            }
          },
          align: "center",
        },
        {
          title: '开标时间',
          dataIndex: 'openBiddingDate',
          customRender: function(text) {
            return !text ? "" : (text.length > 10 ? text.substr(0, 10) : text)
          },
          width: 120,
          align: "center",
        },
        {
          title: '操作',
          dataIndex: 'action',
          width: 147,
          scopedSlots: {
            customRender: 'action'
          },
          align: "center",
        }
      ],
      dataSource: [

      ],
      url: {
        list: "/srm/biddingMain/list",
        delete: "/srm/biddingMain/delete",
        deleteBatch: "/srm/biddingMain/deleteBatch",
        exportXlsUrl: "/srm/biddingMain/exportXls",
        importExcelUrl: "srm/biddingMain/importExcel",

      },
      dictOptions: {},
      superFieldList: [],
    }
  },
  created() {
    this.loadData(1);
  },
  computed: {
    importExcelUrl: function() {
      return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
    }
  },
  methods: {
    callback(tabKey){
      let that = this;
      if(tabKey == '2'){
        setTimeout(() => {
          that.$refs.bargin.loadData();
        }, 300)
      }else{
        that.searchReset();
      }
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
      if(this.tabKey == '0'){
        params.biddingStatus = '0';
      }else if(this.tabKey == '1'){
        params.biddingStatus = '1,2,3,4,5,6,7,8';
      }
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
          'font-family': 'Microsoft YaHei',
        },
      }
    },
    handleAccept(record) {
      let url = "/srm/biddingMain/updStatus";
      let param = {
        id:record.id,
        status:'2'
      }
      putAction(url,param).then(res => {
        this.$message.success("操作成功");
        this.searchQuery();
      })
    },
    handleReject(id) {
      let url = "/srm/biddingMain/updStatus";
      let param = {
        id:id,
        status:'1'
      }
      putAction(url,param).then(res => {
        this.$message.success("操作成功");
        this.searchQuery();
      })
    },

  }
}
</script>
<style scoped>
@import '~@assets/less/common.less';
</style>
<style lang="less" scoped>
.ant-row.ant-form-item {
  margin-bottom: 12px;
}
</style>
