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
    <a-button @click="handleSet" style="float: right;z-index: 999" type="primary">批量设置交期</a-button>
    <a-table
      ref="table"
      size="middle"
      bordered
      class="j-table-force-nowrap"
      :scroll="{x:true,y:700}"
      :columns="model.reqType == '0' ? columns : columns1"
      :dataSource="dataSource"
      :pagination="false">
      <template slot='planDeliveryDate' slot-scope='text,record'>
        <a-date-picker v-model="record.planDeliveryDate" style="width: 100%" />
      </template>
    </a-table>
    <div class="drawer-footer" style="text-align: center;margin-top:15px;">
      <a-button @click="handleCancel" style="margin-bottom: 0;" type="primary">关闭</a-button>
      <a-button v-if="!disableSubmit"  @click="handleOk" type="primary" style="margin-bottom: 0;margin-left: 10px">提交</a-button>
    </div>

    <a-drawer
      title="批量设置交期"
      width="50%"
      placement="right"
      :closable="false"
      :headerStyle="{ textAlign: 'center' }"
      @close="close1"
      destroyOnClose
      :visible="visible1">
        <a-row v-for='(item,index) in pList'>
          <a-col :span='1' style='margin-top: 4px'>
            <span >序号</span>
          </a-col>
          <a-col :span='8'>
            <a-input v-model='item.sort' style='width: 40%'></a-input> - <a-input v-model='item.sort1' style='width: 40%'></a-input>
          </a-col>
          <a-col :span='1' style='margin-top: 4px'>
            <span >交期</span>
          </a-col>
          <a-col :span='8'>
            <a-date-picker v-model="item.planDeliveryDate" style="width: 100%" />
          </a-col>
          <a-col :span='2' style='margin-top: 1px;margin-left: 20px;font-size: 20px'>
            <a-icon type="plus-circle" @click='addRow'/>
            <a-icon type="minus-circle" @click='delRow(index)' style='margin-left: 10px'/>
          </a-col>
        </a-row>
        <div class="drawer-footer" style="float: right;margin-top:100px;">
          <a-button @click="close1" style="margin-bottom: 0;" type="primary">关闭</a-button>
          <a-button @click="handleOk1" type="primary" style="margin-bottom: 0;margin-left: 10px">提交</a-button>
        </div>
    </a-drawer>
  </a-drawer>
<!--  </j-modal> -->
</template>

<script>


import { isNotNullOrEmpty, isNullOrEmpty } from '@/utils/util'

  export default {
    name: 'ContractObjectQtyModal',
    components: {

    },
    data() {
      return {
        pList:[],
        visible1:false,
        form:{},
        spans: {
          labelCol1: {span: 2},
          wrapperCol1: {span: 22},
          //1_5: 分为1.5列（相当于占了2/3）
          labelCol1_5: { span: 3 },
          wrapperCol1_5: { span: 21 },
          labelCol2: {span: 4},
          wrapperCol2: {span: 20},
          labelCol3: {span: 4},
          wrapperCol3: {span: 18},
          labelCol4: {span: 8},
          wrapperCol4: {span: 16},
          labelCol6: {span: 12},
          wrapperCol6: {span: 12},
        },
        columns:[
          {
            title: '序号',
            dataIndex: 'sort',
            width:60,
          },
          {
            title: '设备名称',
            dataIndex: 'prodName',
            width:120,
          },
          {
            title: '规格、型号参数',
            dataIndex: 'speType',
            width:200,
            ellipsis:true
          },
          {
            title: '设备产能',
            dataIndex: 'capacity',
            width:120,
          },
          {
            title: '数量',
            dataIndex: 'qty',
            width:120,
            customRender: function(t, r, index) {
              if(r.unitId_dictText != null && r.unitId_dictText != '' && r.unitId_dictText != undefined){
                return r.qty + r.unitId_dictText;
              }else{
                return r.qty;
              }
            },
          },
          {
            title: '设备单价',
            dataIndex: 'priceTax',
            width:120,
          },
          {
            title: '小计',
            dataIndex: 'amountTax',
            width:120,
          },
          {
            title: '交货日期',
            dataIndex: 'planDeliveryDate',
            width:120,
            scopedSlots: {
              customRender: 'planDeliveryDate'
            },
          },

        ],
        columns1:[
          {
            title: '序号',
            dataIndex: 'sort',
            width:60,
          },
          {
            title: '设备名称',
            dataIndex: 'prodName',
            width:120,
          },
          {
            title: '数量',
            dataIndex: 'qty',
            width:120,
            customRender: function(t, r, index) {
              if(r.unitId_dictText != null && r.unitId_dictText != '' && r.unitId_dictText != undefined){
                return r.qty + r.unitId_dictText;
              }else{
                return r.qty;
              }
            },
          },
          {
            title: '设备单价',
            dataIndex: 'priceTax',
            width:120,
          },
          {
            title: '小计',
            dataIndex: 'amountTax',
            width:120,
          },
          {
            title: '交货日期',
            dataIndex: 'planDeliveryDate',
            width:120,
            scopedSlots: {
              customRender: 'planDeliveryDate'
            },
          },

        ],
        dataSource:[],
        title:'数量拆分',
        width: '90%',
        visible: false,
        disableSubmit: false,
        model:{},
      }
    },
    methods:{
      handleOk1(){
        for(let i = 0; i < this.pList.length; i++){
          let row = this.pList[0];
          if(isNullOrEmpty(row.sort)){
            this.$message.error('第' + (i + 1) + "行,开始区间必填");
            return;
          }
          if(isNullOrEmpty(row.sort1)){
            this.$message.error('第' + (i + 1) + "行,结束区间必填");
            return;
          }
          if(row.sort > row.sort1){
            this.$message.error('第' + (i + 1) + "行,开始区间不能大于结束时间");
            return;
          }
          if(isNullOrEmpty(row.planDeliveryDate)){
            this.$message.error('第' + (i + 1) + "行,交期必填");
            return;
          }
        }

        for(let i = 0; i < this.dataSource.length; i++){
          let idx = this.dataSource[i].sort;
          let planDeliveryDate = null;
          for(let j = 0; j < this.pList.length; j++){
            let child = this.pList[j];
            if(child.sort <= idx && idx <= child.sort1){
              planDeliveryDate = child.planDeliveryDate;
              break;
            }
          }
          if(isNotNullOrEmpty(planDeliveryDate)){
            this.dataSource[i].planDeliveryDate = planDeliveryDate;
          }
        }

        this.close1();
      },
      delRow(index){
        if(this.pList.length == 1){
          this.$message.error('必须保留一行');
          return;
        }
        this.pList.splice(index,1);
      },
      addRow(){
        let row = {
          sort:'',
          sort1:'',
          planDeliveryDate:''
        }
        this.pList.push(row);
      },
      close1(){
        this.visible1 = false;
      },
      handleSet(){
        this.pList = [];
        let row = {
          sort:'',
          sort1:'',
          planDeliveryDate:''
        }
        this.pList.push(row);
        this.visible1 = true;
      },
      add (record) {
        this.visible = true;
        this.model = record;
        this.dataSource = [];
        // let childList = record.childList;
        // if(childList == null || childList.length == 0){
        //   childList = [];
        //   for(let i = 0; i < record.qty; i++){
        //     let json = JSON.parse(JSON.stringify(record));
        //     json.amountTax = json.priceTax;
        //     json.amount = json.price;
        //     json.qty = 1;
        //     json.sort = i + 1;
        //     childList.push(json);
        //     console.log(json);
        //   }
        // }
        let childList = [];
        let objList = record.objList;
        if(objList != null && objList.length > 0){
          objList.filter(item => {
            for(let i = 0; i < item.qty; i++){
              let json = JSON.parse(JSON.stringify(item));
              json.amountTax = json.contractPriceTax;
              json.amount = json.contractPrice;
              json.priceTax = json.contractPriceTax;
              json.price = json.contractPrice;
              json.contractTaxRate = json.taxRate;
              json.qty = 1;
              json.sort = i + 1;
              childList.push(json);
              console.log(json);
            }
          })
        }
        this.dataSource = childList;
      },
      handleOk(){
        let that = this;
        for(let i = 0; i < that.dataSource.length; i++){
          if(isNullOrEmpty(that.dataSource[i].planDeliveryDate)){
            that.$message.error("第"+(i+1) + "行,请输入交货日期");
            return;
          }
        }
        that.visible = false;
        that.$emit('ok',that.model,that.dataSource);
      },
      close () {
        this.$emit('close');
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