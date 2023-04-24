<template>
	<a-spin :spinning="confirmLoading">
		<j-form-container >
			<!-- 主表单区域 -->
			<a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
				<a-row>
					<a-divider orientation="left" style="color: #00A0E9">
						招标基本信息
					</a-divider>
					<a-col :span="8">
						<a-form-model-item label="招标名称" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="biddingName">
							<a-input v-model="model.biddingName" placeholder="请输入招标名称" disabled></a-input>
						</a-form-model-item>
					</a-col>
					<a-col :span="8">
						<a-form-model-item label="招标编码" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" prop="biddingNo">
							<a-input v-model="model.biddingNo" placeholder="请输入招标编码" disabled></a-input>
						</a-form-model-item>
					</a-col>
          <a-col :span="8">
            <a-form-model-item label="招标类型" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
                               prop="biddingType">
              <j-dict-select-tag type="select" v-model="model.biddingType" dictCode="bidding_type" placeholder="请输入招标类型" disabled/>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="开标地址" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
                               prop="openBiddingAddress">
              <!--                    <a-input v-model="model.openBiddingAddress" placeholder="请输入开标地址" :disabled="formDisabled"></a-input>-->
              <j-dict-select-tag v-model="model.openBiddingAddress" placeholder="请输入开标地址"
                                 dictCode="sys_depart,depart_name,id,parent_id = '-1'"
                                 disabled />
            </a-form-model-item>
          </a-col>
<!--          <a-col :span="8">-->
<!--            <a-form-model-item label="币种" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" >-->
<!--              <j-dict-select-tag type="select" v-model="model.biddingCurrency" dictCode="currency_type" placeholder="请输入招标币种" disabled/>-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
					<a-col :span="8">
						<a-form-model-item label="招标截至时间" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="biddingDeadline">
							<j-date placeholder="请选择招标截至时间" v-model="model.biddingDeadline" style="width: 100%" disabled/>
						</a-form-model-item>
					</a-col>
          <a-col :span="8">
            <a-form-model-item label="开标时间" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
                               prop="openBiddingDate">
              <j-date placeholder="请选择开标时间" v-model="model.openBiddingDate" style="width: 100%"  disabled/>
            </a-form-model-item>
          </a-col>
        </a-row>
<!--					<a-col :span="8">
						<a-form-model-item label="招标说明" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3"
							prop="biddingDescription">
							<a-input v-model="model.biddingDescription" placeholder="请输入招标说明" disabled></a-input>
						</a-form-model-item>
					</a-col>-->
        <a-row>
          <a-col :span="12">
            <a-form-model-item label="投标须知" :labelCol="spans.labelCol2" :wrapperCol="spans.wrapperCol2">
              <a-textarea v-model="model.zbComment" placeholder="投标须知" disabled style='width: 60%'></a-textarea>
            </a-form-model-item>
          </a-col>
        </a-row>
        <a-row>
          <a-col :span="12">
            <a-form-model-item label="详细规格需求说明" :labelCol="spans.labelCol2" :wrapperCol="spans.wrapperCol2">
              <a-textarea v-model="model.reqComment" placeholder="详细规格需求说明" disabled style='width: 60%'></a-textarea>
            </a-form-model-item>
          </a-col>
        </a-row>
        <a-row>
          <a-col :span="12">
            <a-form-model-item label="招标附件" :labelCol="spans.labelCol2" :wrapperCol="spans.wrapperCol2" >
              <j-upload isMultiple v-model="model.zbAttachment" :disabled="true"></j-upload>
            </a-form-model-item>
          </a-col>
        </a-row>

        <a-row>
          <a-divider orientation="left" style="color: #00A0E9">
            供应商基本信息
          </a-divider>
          <a-col :span="8">
            <a-form-model-item label="供应商名称" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
              {{$store.getters.userInfo.realname}}
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="报价日期" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
              {{model.quoteDate}}
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="报价币种" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" required>
              <j-dict-select-tag style="width:100%;" v-model="model.currency" placeholder="币种"
                                 dictCode="currency_type" :disabled="formDisabled"/>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="税率" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" required>
              <j-dict-select-tag style="width:100%;" v-model="model.taxRate" placeholder="税率"
                                 dictCode="tax_rate" :disabled="formDisabled" @change='setTaxRate'/>
            </a-form-model-item>
          </a-col>
          <a-col :span="8" v-if="model.isSort == '1'">
            <a-form-model-item label="当前报价名次" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
              {{model.priceRank}}
            </a-form-model-item>
          </a-col>
          <a-col :span="8" v-if="model.isSort == '1'">
            <a-form-model-item label="当前交期名次" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3">
              {{model.leadTimeRank}}
            </a-form-model-item>
          </a-col>
          <a-col :span="8" v-if='model.currency != "RMB" && model.currency != null && model.isSort != "1"'>
            <a-form-model-item label="贸易方式" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" required>
              <j-dict-select-tag style="width:100%;" v-model="model.tradeType" placeholder="贸易方式"
                                 dictCode="trade_type" :disabled="formDisabled"/>
            </a-form-model-item>
          </a-col>
        </a-row>
        <a-row v-if="model.isSort == '1'">
          <a-col :span="8" v-if='model.currency != "RMB" && model.currency != null'>
            <a-form-model-item label="贸易方式" :labelCol="spans.labelCol3" :wrapperCol="spans.wrapperCol3" required>
              <j-dict-select-tag style="width:100%;" v-model="model.tradeType" placeholder="贸易方式"
                                 dictCode="trade_type" :disabled="formDisabled"/>
            </a-form-model-item>
          </a-col>
        </a-row>
				<!-- 子表单区域 -->
				<a-divider orientation="left" style="color: #00A0E9">
					招标标的
				</a-divider>
				<a-table
				  ref="table"
				  size="middle"
				  rowKey="id"
				  class="j-table-force-nowrap"
				  :scroll="{x:true, y:500}"
				  :columns="model.reqType == '0' ? columns : columns1"
				  :dataSource="dataSource"
				  :pagination="false"
          @expand="open"
          :expandedRowKeys="expandedRowKeys">

          <div slot="expandedRowRender" slot-scope="text">
            <div class="rfq-content rfq-content2" style="width:400px">
              <table>
                <thead>
                <tr>
                  <th style="width: 80px">序号</th>
                  <th style="width: 200px">配套产品</th>
                  <th style="width: 200px">品牌</th>
                  <th style="width: 200px">规格型号</th>
                  <th style="width: 180px">税率</th>
                  <th style="width: 180px">数量</th>
                  <th style="width: 180px">单价</th>
                  <th style="width: 180px">总价</th>
                  <th style="width: 100px">操作</th>
                </tr>
                </thead>
                <tbody>
                <tr v-for="(item, index) in text.childList">
                  <td style="width: 80px">{{index + 1}}</td>
                  <td style="width: 200px">
                    <a-input v-model='item.prodName' placeholder="配套设备" :disabled="formDisabled"></a-input>
                  </td>
                  <td style="width: 200px">
                    <a-input v-model='item.brandName' placeholder="品牌"  :disabled="formDisabled"></a-input>
                  </td>
                  <td style="width: 200px">
                    <a-input v-model='item.speType' placeholder="规格型号" :disabled="formDisabled"></a-input>
                  </td>
                  <td style="width: 180px">
                    <j-dict-select-tag placeholder="请输入税率" v-model="item.taxRate" dictCode="tax_rate"  :disabled="formDisabled || model.taxRate != '100'"/>
                  </td>
                  <td style="width: 180px">
                    <a-input-number v-model='item.qty' placeholder="数量" style='width: 100%' @change='setVal(item)' :disabled="formDisabled"></a-input-number>
                  </td>
                  <td style="width: 180px">
                    <a-input-number v-model='item.priceTax' placeholder="单价" style='width: 100%' @change='setVal(item)' :disabled="formDisabled"
                                    :formatter="value => `${value}`.replace(/\B(?=(\d{3})+(?!\d))/g, ',')"
                                    :parser="value => value.replace(/\$\s?|(,*)/g, '')"></a-input-number>
                  </td>

                  <td style="width: 180px">
<!--                    {{item.amountTax}}-->
                    <a-input-number v-model='item.amountTax' style='width: 100%'  disabled
                                    :formatter="value => `${value}`.replace(/\B(?=(\d{3})+(?!\d))/g, ',')"
                                    :parser="value => value.replace(/\$\s?|(,*)/g, '')"></a-input-number>
                  </td>
                  <td style="width: 100px">
                    <a @click='delRow(index,text.childList)' :disabled="formDisabled">删除</a>
                  </td>
                </tr>
                </tbody>
              </table>
            </div>
          </div>

          <template slot='comment' slot-scope='text,record'>
            <a-input type='textarea' v-model='record.comment' :disabled="formDisabled" auto-size></a-input>
          </template>

          <template slot='taxRate' slot-scope='text,record'>
            <j-dict-select-tag style="width:100%;" v-model="record.taxRate" placeholder="税率"
                               dictCode="tax_rate" disabled/>
          </template>

          <template slot='supBrandName' slot-scope='text,record'>
            <a-input type='textarea' v-model='record.supBrandName' :disabled="formDisabled" auto-size></a-input>
          </template>

          <template slot='supSpeType' slot-scope='text,record'>
            <a-input type='textarea' v-model='record.supSpeType' :disabled="formDisabled" auto-size></a-input>
          </template>

          <template slot='price' slot-scope='text,record'>
            <a-input-number v-model='record.price' style='width: 100%' :disabled="formDisabled" @change='setChild(record)'
                            :formatter="value => `${value}`.replace(/\B(?=(\d{3})+(?!\d))/g, ',')"
                            :parser="value => value.replace(/\$\s?|(,*)/g, '')"></a-input-number>
          </template>

          <template slot='suppCapacity' slot-scope='text,record'>
            <a-input-number  v-model='record.suppCapacity' style='width: 100%' :disabled="formDisabled"></a-input-number>
          </template>

          <template slot='suppLeadTime' slot-scope='text,record'>
            <a-date-picker v-model="record.suppLeadTime" style="width: 100%" :disabled="formDisabled" :disabled-date="disabledDate1"
                           v-if='record.childList != null && record.childList.length > 1' placeholder="交期"/>
            <a-date-picker v-model="record.suppLeadTime" style="width: 100%" :disabled="formDisabled" :disabled-date="disabledDate"
                           v-else placeholder="交期"/>
          </template>

          <template slot='action' slot-scope='text,record'>
            <a @click='addChild(record)' :disabled="formDisabled">添加明细</a>
          </template>
				</a-table>
        <a-divider orientation="left" style="color: #00A0E9">
          报价说明和附件
        </a-divider>
        <a-form-model-item label="报价说明" :labelCol="spans.labelCol1" :wrapperCol="spans.wrapperCol1">
          <a-input v-model='model.comment' :disabled="formDisabled" type='textarea' :rows="3" style='width: 27%'></a-input>
        </a-form-model-item>
        <a-form-model-item label="报价文件" :labelCol="labelCol1" :wrapperCol="wrapperCol1" required style='margin-left: -10%'>
          <j-upload isMultiple v-model="model.attachment" :disabled="formDisabled"></j-upload>
        </a-form-model-item>
        <a-form-model-item label="配置规格文件" :labelCol="labelCol1" :wrapperCol="wrapperCol1" required style='margin-left: -10%'>
          <j-upload isMultiple v-model="model.otherAttachment" :disabled="formDisabled"></j-upload>
        </a-form-model-item>

			</a-form-model>
		</j-form-container>
	</a-spin>
</template>

<script>
import { JVxeTableModelMixin } from '@/mixins/JVxeTableModelMixin.js'
import JFormContainer from '@/components/jeecg/JFormContainer'
import { billListMixin } from '../../mixins/billListMixin'
import { billModalMixin } from '../../mixins/billModalMixin'
import { nowDate, isNullOrEmpty, isNotNullOrEmpty, preciseNum } from '@/utils/util'
import { getAction,httpAction } from '@/api/manage'
import moment from 'moment';

export default {
		name: 'BiddingMainForm',
		mixins: [JVxeTableModelMixin,billListMixin, billModalMixin],
		components: {
			JFormContainer,
		},
		data() {
			return {
        labelCol1: {span: 4},
        wrapperCol1: {span: 20},
        expandedRowKeys:[],
				labelCol: {
					xs: {
						span: 24
					},
					sm: {
						span: 5
					},
				},
				wrapperCol: {
					xs: {
						span: 24
					},
					sm: {
						span: 16
					},
				},
				model: {},
        columns1: [
          {
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
            title: '标的名称',
            align: "center",
            dataIndex: 'prodName',
            width: 160,
          },
          {
            title: '标的标识',
            align: "center",
            dataIndex: 'prodCode',
            width: 120,
          },
          // {
          //   title: '规格、型号参数',
          //   align: "center",
          //   dataIndex: 'speType',
          //   width: 120,
          // },
          // {
          //   title: '产能要求',
          //   align: "center",
          //   dataIndex: 'capacity',
          //   width: 120,
          // },
          {
            title: '数量',
            align: "center",
            dataIndex: 'qty',
            customRender: (value, row, index) => { //表体的数据列样式
              return row.qty;
            },
            width: 120,
          },
          {
            title: '交货日期',
            align: "center",
            dataIndex: 'leadTime',
            width: 120,
          },
          {
            title: '税率',
            align: "center",
            dataIndex: 'taxRate',
            width: 120,
            scopedSlots: {
              customRender: 'taxRate'
            },
          },
          {
            title: '单价',
            align: "center",
            dataIndex: 'price',
            scopedSlots: {
              customRender: 'price'
            },
            width: 120,
          },
          // {
          //   title: '产能',
          //   align: "center",
          //   dataIndex: 'suppCapacity',
          //   scopedSlots: {
          //     customRender: 'suppCapacity'
          //   },
          //   width: 120,
          // },
          {
            title: '交期',
            align: "center",
            dataIndex: 'suppLeadTime',
            scopedSlots: {
              customRender: 'suppLeadTime'
            },
            width: 140,
          },
          {
            title: '备注',
            align: "center",
            dataIndex: 'comment',
            scopedSlots: {
              customRender: 'comment'
            },
            width: 160,
          },
          {
            title: '操作',
            align: "center",
            dataIndex: 'action',
            scopedSlots: {
              customRender: 'action'
            },
            width: 100,
          },
        ],
				columns: [
				  {
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
            width: 160,
          },
					{
						title: '设备标识',
						align: "center",
						dataIndex: 'prodCode',
            width: 120,
					},
          {
            title: '规格、型号参数',
            align: "center",
            dataIndex: 'speType',
            width: 120,
            ellipsis:true
          },
					{
						title: '产能要求',
						align: "center",
						dataIndex: 'capacity',
            width: 120,
					},

					{
						title: '数量',
						align: "center",
						dataIndex: 'qty',
						customRender: (value, row, index) => { //表体的数据列样式
							return row.qty + row.unitId_dictText
						},
            width: 120,
					},
					{
						title: '交货日期',
						align: "center",
						dataIndex: 'leadTime',
            width: 120,
					},
          {
            title: '报价规格型号',
            align: "center",
            dataIndex: 'supSpeType',
            width: 120,
            scopedSlots: {
              customRender: 'supSpeType'
            },
          },
          {
            title: '报价品牌',
            align: "center",
            dataIndex: 'supBrandName',
            width: 120,
            scopedSlots: {
              customRender: 'supBrandName'
            },
          },
          {
            title: '税率',
            align: "center",
            dataIndex: 'taxRate',
            width: 120,
            scopedSlots: {
              customRender: 'taxRate'
            },
          },
          {
            title: '单价',
            align: "center",
            dataIndex: 'price',
            scopedSlots: {
              customRender: 'price'
            },
            width: 120,
          },
          {
            title: '产能(万片/月)',
            align: "center",
            dataIndex: 'suppCapacity',
            scopedSlots: {
              customRender: 'suppCapacity'
            },
            width: 120,
          },
          {
            title: '交期',
            align: "center",
            dataIndex: 'suppLeadTime',
            scopedSlots: {
              customRender: 'suppLeadTime'
            },
            width: 130,
          },
          {
            title: '备注',
            align: "center",
            dataIndex: 'comment',
            scopedSlots: {
              customRender: 'comment'
            },
            width: 160,
          },
          {
            title: '操作',
            align: "center",
            dataIndex: 'action',
            scopedSlots: {
              customRender: 'action'
            },
            width: 100,
          },
				],
				// 新增时子表默认添加几行空数据
				validatorRules: {},
				dataSource: [

				],
				url: {
					add: "/srm/biddingMain/add",
				}
			}
		},
		props: {
			//表单禁用
			disabled: {
				type: Boolean,
				default: false,
				required: false
			}
		},
		computed: {
			formDisabled() {
				return this.disabled
			},
		},
		created() {},
		methods: {
      setChild(record){
        let childList = record.childList;
        if(childList != null && childList.length == 1){
          childList.filter(item => {
            item.priceTax = record.price;
            item.amountTax = item.priceTax * item.qty;
            this.$forceUpdate();
          })

        }
      },
      setTaxRate(){
        let taxRate = this.model.taxRate;
        if(isNotNullOrEmpty(taxRate)){
          for(let i = 0; i < this.dataSource.length; i++){
            this.dataSource[i].taxRate = taxRate;
            let childList = this.dataSource[i].childList;
            if(childList != null && childList.length > 0){
              childList.filter(item => {
                item.taxRate = taxRate;
              })
            }
            this.$forceUpdate();
          }
        }
      },
      moment,
      disabledDate1(current) {
        // Can not select days before today and today
        return current && current < moment().subtract(1, 'days').endOf('day')
      },
      disabledDate(current) {
        // Can not select days before today and today
        return current && current < moment().endOf('day');
      },
      addChild(row){
        this.expandedRowKeys = []
        this.expandedRowKeys.push(row.id);
        let child = {
          prodName:'',
          brandName:'',
          speType:'',
          qty:0,
          priceTax:'',
          amountTax:'',
          taxRate : row.taxRate
        }
        if(row.childList == null || row.childList.length == 0){
          row.childList = [];
        }
        row.childList.push(child);
        this.$forceUpdate();
      },
      delRow(index,childList){
        let that = this;
        that.$confirm({
          title:"确认操作",
          content:"是否确认删除?",
          onOk: function(){
            childList.splice(index,1);
          }
        });
      },
      setVal(item){
        let qty = item.qty;
        let priceTax = item.priceTax;
        let fareAmount = item.fareAmount;
        if(isNullOrEmpty(fareAmount)){
          fareAmount = 0;
        }
        if(isNotNullOrEmpty(qty) && isNotNullOrEmpty(priceTax)){
          let amountTax = preciseNum(Number(qty) * Number(priceTax) + Number(fareAmount),2);
          item.amountTax = Number(amountTax);
        }
        this.$forceUpdate();
      },
      open(expanded, row) {
        if (expanded) {
          this.expandedRowKeys = []
          this.expandedRowKeys.push(row.id)
        } else {
          this.expandedRowKeys = []
        }
      },
      handleOk(){
        const that = this
        // 触发表单验证
        that.$refs.form.validate(valid => {
          if (valid) {
            let post = "";
            let url = "";
            post = 'post';
            url = this.url.add;

            let taxRate = that.model.taxRate;
            if(isNullOrEmpty(taxRate)){
              that.$message.warning("请选择税率");
              return;
            }
            let currency = that.model.currency;
            if(isNullOrEmpty(currency)){
              that.$message.warning("请选择币种");
              return;
            }
            let tradeType = that.model.tradeType;
            if(isNullOrEmpty(tradeType) && currency != 'RMB'){
              that.$message.warning("请选择贸易方式");
              return;
            }


            let dataSource = that.dataSource;
            if(dataSource == null || dataSource.length == 0){
              that.$message.warning("标的需求不能为空");
              return;
            }
            for(let i = 0; i < dataSource.length; i++){
              if(isNullOrEmpty(dataSource[i].price)){
                that.$message.warning("第" + (i+1) + "行,单价为空");
                return;
              }
              if(that.model.reqType == '0'){
                if(isNullOrEmpty(dataSource[i].suppCapacity)){
                  that.$message.warning("第" + (i+1) + "行,产能为空");
                  return;
                }
                if(isNullOrEmpty(dataSource[i].suppLeadTime)){
                  that.$message.warning("第" + (i+1) + "行,交期为空");
                  return;
                }
              }
              let amountTax = Number(dataSource[i].price) * Number(dataSource[i].qty);
              let cAmountTax = 0;
              let childList = dataSource[i].childList;
              if(childList != null && childList.length > 0){
                for(let j = 0; j < childList.length; j++){
                  if(isNullOrEmpty(childList[j].prodName)){
                    that.$message.error("第" + (i+1)+"行,子项产品为空");
                    return;
                  }
                  if(isNullOrEmpty(childList[j].qty)){
                    that.$message.error("第" + (i+1)+"行,子项产品数量为空");
                    return;
                  }
                  if(isNullOrEmpty(childList[j].priceTax)){
                    that.$message.error("第" + (i+1)+"行,子项产品单价为空");
                    return;
                  }
                  if(isNullOrEmpty(childList[j].taxRate)){
                    that.$message.error("子项产品税率为空");
                    return;
                  }
                  if(childList[j].taxRate == 100){
                    that.$message.error("子项产品税率不能为其他");
                    return;
                  }
                  cAmountTax = Number(cAmountTax) + Number(childList[j].amountTax);
                }
                if(cAmountTax != amountTax){
                  that.$message.error("子项产品金额能与明细行金额不相等");
                  return;
                }
              }
            }
            let attachment = that.model.attachment;
            if(isNullOrEmpty(attachment)){
              that.$message.warning("请上传附件");
              return;
            }
            that.model.detailList = that.dataSource;
            that.$confirm({
              content: `是否确认提交`,
              onOk: () => {
                httpAction(url,that.model,post).then((res)=> {
                  if (res.success) {
                    that.$message.success(res.message);
                    that.$emit('ok');
                  } else {
                    that.$message.warning(res.message);
                  }
                })
              }
            })
          }else{
            return false;
          }
        })
      },
			edit(record){
        this.model = Object.assign({}, record);
        this.model.comment = "";
        this.model.attachment = "";
        this.model.zbAttachment = record.attachment;
        this.model.reqComment = record.reqComment;
        this.model.isSort = record.isSort;
        this.model.zbComment = record.comment;
        //报价记录
        this.fetchQuote(record.id);
        this.model.quoteDate = nowDate(new Date(),'yyyy-MM-dd');
        //获取招标明细
        this.fetchDetailList(record.id);
        //交期名次
        this.getLeadTimeRank(record.id);
        //报价名次
        this.getPriceRank(record.id);
      },
      getPriceRank(id){
        let url = "/srm/biddingMain/getPriceRank";
        let param = {
          id:id
        }
        getAction(url,param).then(res => {
          if(res.result != null){
            this.model.priceRank = res.result;
          }
        })
      },
      getLeadTimeRank(id){
        let url = "/srm/biddingMain/getLeadTimeRank";
        let param = {
          id:id
        }
        getAction(url,param).then(res => {
          if(res.result != null){
            this.model.leadTimeRank = res.result;
          }
        })
      },
      fetchQuote(id){
        let url = "/srm/biddingMain/fetchQuote";
        let param = {
          id:id
        }
        getAction(url,param).then(res => {
          if(res.result != null){
            this.model.currency = res.result.currency;
            this.model.taxRate = res.result.taxRate;
            this.model.payMethod = res.result.payMethod;
            this.model.quoteDate = res.result.createTime;
            this.model.attachment = res.result.attachment;
            this.model.otherAttachment = res.result.otherAttachment;
            this.model.comment = res.result.comment;
            this.model.tradeType = res.result.tradeType;
          }
        })
      },
      fetchDetailList(id){
			  let url = "/srm/biddingMain/fetchDetailList";
			  let param = {
			    id:id
        }
        getAction(url,param).then(res => {
          this.dataSource = res.result;
        })
      }
		}
	}
</script>

<style lang="less" scoped>
	.ant-row.ant-form-item {
		margin-bottom: 12px;
	}
</style>
<style>
rfq-content {
  min-height: 50px;
  display: flex;
}
.rfq-content table {
  width: 100%;
  margin-left: 5px;
  margin-right: 5px;
  border: 1px #ccc solid;
}
.rfq-content table thead th {
  text-align: center;
  color: rgba(0, 0, 0, 0.85);
  font-weight: 500;
  background: #fafafa;
  border: 1px solid #e8e8e8;
  transition: background 0.3s ease;
}
.rfq-content table tbody tr td {
  font-size: 12px;
  border-right: 1px #ccc solid;
  padding-left: 5px;
  padding-right: 5px;
  padding-top: 5px;
  padding-bottom: 5px;
  border-bottom: 1px #ccc solid;
}
</style>
