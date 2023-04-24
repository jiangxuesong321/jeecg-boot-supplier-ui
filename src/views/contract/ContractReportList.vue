<template>
  <a-card :bordered="false">
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :span="6">
            <a-form-item label="合同编码" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
              <a-input placeholder="请输入合同编码" v-model="queryParam.contractNumber"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="6">
            <a-form-item label="合同名称" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
              <a-input placeholder="请输入合同名称" v-model="queryParam.contractName"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span='6'>
            <a-button type='primary' @click='searchQuery' icon='search'>查询</a-button>
            <a-button @click='searchReset' icon='reload' style='margin-left: 8px' type='primary'>重置</a-button>
            <a-button type="primary" icon="download" @click="handleExportXls('合同执行进度')" style='margin-left: 8px'>导出</a-button>
          </a-col>
        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator">

    </div>

    <div>

      <a-table ref="table" size="middle" rowKey="id" :scroll="{x:true,y:500}"
               :columns="columns" :dataSource="dataSource" :pagination="ipagination" :loading="loading"
               @change="handleTableChange">
        <template slot='payMethod' slot-scope='text,record'>
          <div v-if="text != null && text != undefined && text != ''">
            <div v-for='(item,index) in text.split(";")' v-if='item != null && item != undefined && item != ""'>
              {{item}}
            </div>
          </div>
        </template>

        <template slot='payAmount' slot-scope='text,record'>
          <div v-if="text != null && text != undefined && text != ''">
            <div v-for='(item,index) in text.split(";")' v-if='item != null && item != undefined && item != ""'>
              <span v-if="record.contractCurrency == 'RMB'">
                    ¥
              </span>
              <span v-if="record.contractCurrency == 'JPY'">
                    Ұ
              </span>
              <span v-if="record.contractCurrency == 'USD'">
                    $
              </span>
              <span v-if="record.contractCurrency == 'EUR'">
                    €
              </span>
              {{iegAmount(Math.trunc(item),'total')}}
            </div>
          </div>
        </template>

        <template slot='payRate' slot-scope='text,record'>
          <div v-if="text != null && text != undefined && text != ''">
            <div v-for='(item,index) in text.split(";")' v-if='item != null && item != undefined && item != ""'>
              {{item}}%
            </div>
          </div>
        </template>

        <template slot='invoiceAmount' slot-scope='text,record'>
          <div v-if="text != null && text != undefined && text != ''">
            <div v-for='(item,index) in text.split(";")' v-if='item != null && item != undefined && item != ""'>
              <span v-if="record.contractCurrency == 'RMB'">
                    ¥
              </span>
              <span v-if="record.contractCurrency == 'JPY'">
                    Ұ
              </span>
              <span v-if="record.contractCurrency == 'USD'">
                    $
              </span>
              <span v-if="record.contractCurrency == 'EUR'">
                    €
              </span>
              {{iegAmount(Math.trunc(item),'total')}}
            </div>
          </div>
        </template>

        <template slot='invoiceNo' slot-scope='text,record'>
          <div v-if="text != null && text != undefined && text != ''">
            <div v-for='(item,index) in text.split(";")' v-if='item != null && item != undefined && item != ""' style="width: 160px;overflow: hidden;white-space: nowrap;text-overflow: ellipsis;" :title="text">
              {{item}}
            </div>
          </div>
        </template>

      </a-table>
    </div>


  </a-card>
</template>

<script>
import { JeecgListMixin } from '@/mixins/JeecgListMixin'
import '@/assets/less/TableExpand.less'
import { billListMixin } from '../mixins/billListMixin'
import { billModalMixin } from '../mixins/billModalMixin'
import { getAction } from '@api/manage'
import { iegAmount } from '@/utils/util'
export default {
  name: "ContractBaseList",
  mixins: [JeecgListMixin, billListMixin, billModalMixin],
  components: {

  },
  data() {
    return {
      iegAmount,
      description: '合同执行进度',
      queryParam: {
        contractNumber: '',
        contractName: '',
      },
      // 表头
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
          title: '合同编号',
          align: "center",
          dataIndex: 'contractNumber',
          scopedSlots: {customRender: 'contractNumber'},
          width: 140,
        },
        {
          title: '合同名称',
          align: "center",
          dataIndex: 'contractName',
          width: 160,
        },
        {
          title: '合同签订日期',
          align: "center",
          dataIndex: 'contractSignDate',
          width: 120,
        },
        {
          title: '设备名称',
          align: "center",
          dataIndex: 'prodName',
          width: 120,
        },
        {
          title: '设备型号',
          align: "center",
          dataIndex: 'prodSpecType',
          width: 120,
        },
        {
          title: '数量',
          align: "center",
          dataIndex: 'qty',
          width: 120,
        },
        {
          title: '单价',
          align: "center",
          dataIndex: 'contractPriceTax',
          width: 120,
          customRender:function (t,r,index) {
            let icon = "";
            if(r.contractCurrency == 'RMB'){
              icon = '¥';
            }else if(r.contractCurrency == 'JPY'){
              icon = 'Ұ';
            }else if(r.contractCurrency == 'USD'){
              icon = '$';
            }else if(r.contractCurrency == 'EUR'){
              icon = '€';
            }
            // return icon + iegAmount(t,'total')
            const obj = {
              children: icon + iegAmount(Math.trunc(t),'total'),
              attrs: {},
            };
            obj.attrs.align = 'right';//控制表体内容居右
            return obj;
          }
        },
        {
          title: '总额',
          align: "center",
          dataIndex: 'contractAmountTax',
          width: 120,
          customRender:function (t,r,index) {
            let icon = "";
            if(r.contractCurrency == 'RMB'){
              icon = '¥';
            }else if(r.contractCurrency == 'JPY'){
              icon = 'Ұ';
            }else if(r.contractCurrency == 'USD'){
              icon = '$';
            }else if(r.contractCurrency == 'EUR'){
              icon = '€';
            }
            // return icon + iegAmount(t,'total')
            const obj = {
              children: icon + iegAmount(Math.trunc(t),'total'),
              attrs: {},
            };
            obj.attrs.align = 'right';//控制表体内容居右
            return obj;
          }
        },
        {
          title: '收款方式',
          dataIndex: 'payMethod',
          align: "center",
          scopedSlots: {customRender: 'payMethod'},
          width: 120,
        },
        {
          title: '收款金额',
          dataIndex: 'payAmount',
          align: "center",
          scopedSlots: {customRender: 'payAmount'},
          width: 120,
        },
        {
          title: '收款比例(%)',
          dataIndex: 'payRate',
          align: "center",
          width: 120,
          scopedSlots: {customRender: 'payRate'},
        },
        {
          title: '发货数量',
          dataIndex: 'sqty',
          width: 120,
          align: "center",
        },
        {
          title: '开票金额',
          dataIndex: 'invoiceAmount',
          width: 120,
          align: "center",
          scopedSlots: {customRender: 'invoiceAmount'},
        },
        {
          title: '开票号码',
          dataIndex: 'invoiceNo',
          width: 160,
          align: "center",
          scopedSlots: {customRender: 'invoiceNo'},
        },
      ],
      url: {
        list: "/srm/contractBase/fetchContractReport",
        exportXlsUrl: "/srm/contractBase/exportContractReport",
      },
      dictOptions: {},
      superFieldList: [],
    }
  },
  created() {

  },
  computed: {
    importExcelUrl: function() {
      return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
    }
  },
  methods: {
    toSend(record){
      this.$refs.sendModal.view(record);
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
      params.isTodo = '1';
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
    handleApprove(record) {
      this.$refs.approveForm.view(record)
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
