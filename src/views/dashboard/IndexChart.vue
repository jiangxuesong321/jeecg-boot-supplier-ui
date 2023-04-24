<template>
	<div class="page-header-index-wide">
		<a-row :gutter="24">
			<a-col :sm="24" :md="17" :xl="17" :style="{ marginBottom: '24px' }">
				<a-row :gutter="24" style="margin-left:1px;">
					<a-col :span="12" style="padding-left:0px;padding-right: 0px;width:50%">
						<div>
							<div class="bidding-rate">
								<div style="width:50%;float:left">
									<span style="color:#FFFFFF;width:100%;margin-left:25px;font-size:18px;">中标率</span>
								</div>
								<div style="width:50%;float:right">
									<img src="~@/assets/diagram_images/bidding_rate.png" width="80px" height="56px" />
									<span style="color:#FFFFFF;width:100%;margin-left:25px;font-size:24px;">{{bidding.ratio}}%</span>
								</div>
							</div>
							<div class="bidding-rate-bottom">
								<div style="width:50%;float:left">
									<span style="color:#71747D;width:100%;margin-left:25px;font-size:14px;">投标项目数：<span
											style="color:#FF8F1F">{{bidding.totalNum}}</span></span>
								</div>
								<div style="width:50%;float:right">
									<span style="color:#71747D;width:100%;margin-left:25px;font-size:14px;">中标项目数：<span
											style="color:#FF8F1F">{{bidding.biddingNum}}</span></span>
								</div>
							</div>
						</div>
					</a-col>
					<a-col :span="12" style="padding-left:20px;padding-right: 0px;width:50%">
						<div>
							<div class="excute-rate">
								<div style="width:50%;float:left">
									<span style="color:#FFFFFF;width:100%;margin-left:25px;font-size:18px;">合同回款率</span>
								</div>
								<div style="width:50%;float:right">
									<img src="~@/assets/diagram_images/contract_rate.png" width="80px" height="56px" />
									<span style="color:#FFFFFF;width:100%;margin-left:25px;font-size:24px;">{{contract.ratio}}%</span>
								</div>
							</div>
							<div class="excute-rate-bottom">
								<div style="width:50%;float:left">
									<span style="color:#71747D;width:100%;margin-left:25px;font-size:14px;">进行中的合同：<span
											style="color:#3662EC">
                    <router-link :to="`/srm/contract`">
                      <a>{{contract.onGoingNum}}</a>
                    </router-link>

                  </span></span>
								</div>
								<div style="width:50%;float:right">
									<span style="color:#71747D;width:100%;margin-left:25px;font-size:14px;">已完成的合同：<span
											style="color:#3662EC">
                    <router-link :to="`/srm/contract`">
                      <a>{{contract.completeNum}}</a>
                    </router-link>
                  </span></span>
								</div>
							</div>
						</div>
					</a-col>
				</a-row>
				<a-row :gutter="24" style="margin-top:10px;margin-left:1px;">
					<a-card class="card-main-index" style="height: 300px!important;">
						<div style="height:32px;">
							<div style="float:left;width:50%;height:32px;">
								<span class='font-title'>合同收款进度</span>
							</div>
              <j-dict-select-tag v-model="queryParam.id" placeholder="请选择合同"
                                 style='width: 20%;float: right'
                                 @change='fetchPayList'
                                 :dictCode="dictCode" />
						</div>
						<div style="margin-top:10px;">
							<div v-for="(record,index) in dataSource"
								style="width:100%;display:-webkit-inline-box;line-height:30px;margin-top:2px;">
								<div style="width:50%;">{{record.contractName}}({{record.contractNumber}})</div>
								<div style="width:50%;">
									<a-progress :stroke-color="{
																'0%': '#FFC300',
																'100%': '#00B578',
									}" :percent="record.payProcess" />
								</div>
							</div>
						</div>
					</a-card>
				</a-row>
			</a-col>
      <a-col :sm="24" :md="7" :xl="7" style='padding-left: 0px;!important;padding-right: 0px'>
        <a-col :span='24'>
          <a-card class="card-main-index" style='height: 150px !important;'>
            <div>
              <div>
                <div class="font-title" style='margin-top: -30px;margin-left: -2px;margin-bottom: 5px;'>合同总额</div>
<!--                <a-col :span='12' v-if='isNotNullOrEmpty(model.contractAmountRmb)'>-->
<!--                  <span style='font-size: 15px'>¥{{ iegAmount(Math.trunc(model.contractAmountRmb), 'total') }}</span>-->
<!--                </a-col>-->
<!--                <a-col :span='12' v-if='isNotNullOrEmpty(model.contractAmountUsd)'>-->
<!--                  <span style='font-size: 15px'>${{ iegAmount(Math.trunc(model.contractAmountUsd), 'total') }}</span>-->
<!--                </a-col>-->
<!--                <a-col :span='12' style='margin-top: 10px;' v-if='isNotNullOrEmpty(model.contractAmountEur)'>-->
<!--                  <span style='font-size: 15px'>€{{ iegAmount(Math.trunc(model.contractAmountEur), 'total') }}</span>-->
<!--                </a-col>-->
<!--                <a-col :span='12' style='margin-top: 10px;' v-if='isNotNullOrEmpty(model.contractAmountJpy)'>-->
<!--                  <span style='font-size: 15px'>￥{{ iegAmount(Math.trunc(model.contractAmountJpy), 'total') }}</span>-->
<!--                </a-col>-->

                <table style='width: 100%;'>
                  <tr>
                    <td style='width: 50%' >
<!--                      CNY-->
                      <span class='rmb-money'></span>
<!--                      <span style='font-size: 16px;font-weight: bolder; color:#140F26;margin-left: 4px'>-->
                      <span  style='font-size: 18px;font-weight: bolder;color: rgb(20, 15, 38);padding-top: -29px;float: right;position: absolute;margin-top: 3px;margin-left: 10px;'>
<!--                        ¥-->
                        {{ iegAmount(Math.trunc(model.contractAmountRmb), 'total') }}
                      </span>
                    </td>
                    <td style='width: 50%' class="lastCol">
<!--                      USD-->
<!--                      <span style='font-size: 16px;font-weight: bolder; color:#140F26;margin-left: 4px'>-->
                      <span class='usd-money'></span>
                      <span  style='font-size: 18px;font-weight: bolder;color: rgb(20, 15, 38);padding-top: -29px;float: right;position: absolute;margin-top: 3px;margin-left: 10px;'>
<!--                        $-->
                        {{ iegAmount(Math.trunc(model.contractAmountUsd), 'total') }}
                      </span>
                    </td>
                  </tr>
                  <tr class="lastrow">
                    <td style='width: 50%'>
<!--                      EUR-->
<!--                      <span style='font-size: 16px;font-weight: bolder; color:#140F26;margin-left: 5px'>-->
                      <span class='eur-money'></span>
                      <span  style='font-size: 18px;font-weight: bolder;color: rgb(20, 15, 38);padding-top: -29px;float: right;position: absolute;margin-top: 3px;margin-left: 10px;'>
<!--                        €-->
                        {{ iegAmount(Math.trunc(model.contractAmountEur), 'total') }}
                      </span>
                    </td>
                    <td style='width: 50%' class="lastCol">
<!--                      JPY-->
<!--                      <span style='font-size: 16px;font-weight: bolder; color:#140F26;margin-left: 8px'>-->
                      <span class='jpy-money'></span>
                      <span  style='font-size: 18px;font-weight: bolder;color: rgb(20, 15, 38);padding-top: -29px;float: right;position: absolute;margin-top: 3px;margin-left: 10px;'>
<!--                        ￥-->
                        {{ iegAmount(Math.trunc(model.contractAmountJpy), 'total') }}
                      </span>
                    </td>
                  </tr>
                </table>
              </div>
            </div>
          </a-card>
        </a-col>
        <a-col :span='24' style='margin-top: 10px'>
          <a-card class="card-main-index" style='height: 150px !important;'>
            <div >
              <div>
                <div class="font-title" style='margin-top: -30px;margin-left: -2px;margin-bottom: 5px;'>收款总额</div>
<!--                <a-col :span='12' v-if='isNotNullOrEmpty(model.payAmountRmb)'>-->
<!--                  <span style='font-size: 15px'>¥{{ iegAmount(Math.trunc(model.payAmountRmb), 'total') }}</span>-->
<!--                </a-col>-->
<!--                <a-col :span='12' v-if='isNotNullOrEmpty(model.payAmountUsd)'>-->
<!--                  <span style='font-size: 15px'>${{ iegAmount(Math.trunc(model.payAmountUsd), 'total') }}</span>-->
<!--                </a-col>-->
<!--                <a-col :span='12' style='margin-top: 10px;' v-if='isNotNullOrEmpty(model.payAmountEur)'>-->
<!--                  <span style='font-size: 15px'>€{{ iegAmount(Math.trunc(model.payAmountEur), 'total') }}</span>-->
<!--                </a-col>-->
<!--                <a-col :span='12' style='margin-top: 10px;' v-if='isNotNullOrEmpty(model.payAmountJpy)'>-->
<!--                  <span style='font-size: 15px'>￥{{ iegAmount(Math.trunc(model.payAmountJpy), 'total') }}</span>-->
<!--                </a-col>-->
                <table style='width: 100%;'>
                  <tr>
                    <td style='width: 50%' >
<!--                      CNY-->
<!--                      <span style='font-size: 16px;font-weight: bolder; color:#140F26;margin-left: 4px'>-->
<!--                        ¥-->
                      <span class='rmb-money'></span>
                      <span  style='font-size: 18px;font-weight: bolder;color: rgb(20, 15, 38);padding-top: -29px;float: right;position: absolute;margin-top: 3px;margin-left: 10px;'>
                        {{ iegAmount(Math.trunc(model.payAmountRmb), 'total') }}
                      </span>
                    </td>
                    <td style='width: 50%' class="lastCol">
<!--                      USD-->
<!--                      <span style='font-size: 16px;font-weight: bolder; color:#140F26;margin-left: 4px'>-->
<!--                        $-->
                      <span class='usd-money'></span>
                      <span  style='font-size: 18px;font-weight: bolder;color: rgb(20, 15, 38);padding-top: -29px;float: right;position: absolute;margin-top: 3px;margin-left: 10px;'>
                        {{ iegAmount(Math.trunc(model.payAmountUsd), 'total') }}
                      </span>
                    </td>
                  </tr>
                  <tr class="lastrow">
                    <td style='width: 50%'>
<!--                      EUR-->
<!--                      <span style='font-size: 16px;font-weight: bolder; color:#140F26;margin-left: 5px'>-->
<!--                        €-->
                      <span class='eur-money'></span>
                      <span  style='font-size: 18px;font-weight: bolder;color: rgb(20, 15, 38);padding-top: -29px;float: right;position: absolute;margin-top: 3px;margin-left: 10px;'>
                        {{ iegAmount(Math.trunc(model.payAmountEur), 'total') }}
                      </span>
                    </td>
                    <td style='width: 50%' class="lastCol">
<!--                      JPY-->
<!--                      <span style='font-size: 16px;font-weight: bolder; color:#140F26;margin-left: 8px'>-->
<!--                        ￥-->
                      <span class='jpy-money'></span>
                      <span  style='font-size: 18px;font-weight: bolder;color: rgb(20, 15, 38);padding-top: -29px;float: right;position: absolute;margin-top: 3px;margin-left: 10px;'>
                        {{ iegAmount(Math.trunc(model.payAmountJpy), 'total') }}
                      </span>
                    </td>
                  </tr>
                </table>
              </div>
            </div>
          </a-card>
        </a-col>
        <a-col :span='24' style='margin-top: 10px'>
          <a-card class="card-main-index" style='height: 150px !important;'>
            <div>
              <div>
                <div class="font-title" style='margin-top: -30px;margin-left: -2px;margin-bottom: 5px;'>开票总额</div>
<!--                <a-col :span='12' v-if='isNotNullOrEmpty(model.invoiceAmountRmb)'>-->
<!--                  <span style='font-size: 15px'>¥{{ iegAmount(Math.trunc(model.invoiceAmountRmb), 'total') }}</span>-->
<!--                </a-col>-->
<!--                <a-col :span='12' v-if='isNotNullOrEmpty(model.invoiceAmountUsd)'>-->
<!--                  <span style='font-size: 15px'>${{ iegAmount(Math.trunc(model.invoiceAmountUsd), 'total') }}</span>-->
<!--                </a-col>-->
<!--                <a-col :span='12' style='margin-top: 10px;' v-if='isNotNullOrEmpty(model.invoiceAmountEur)'>-->
<!--                  <span style='font-size: 15px'>€{{ iegAmount(Math.trunc(model.invoiceAmountEur), 'total') }}</span>-->
<!--                </a-col>-->
<!--                <a-col :span='12' style='margin-top: 10px;' v-if='isNotNullOrEmpty(model.invoiceAmountJpy)'>-->
<!--                  <span style='font-size: 15px'>￥{{ iegAmount(Math.trunc(model.invoiceAmountJpy), 'total') }}</span>-->
<!--                </a-col>-->
                <table style='width: 100%;'>
                  <tr>
                    <td style='width: 50%' >
<!--                      CNY-->
<!--                      <span style='font-size: 16px;font-weight: bolder; color:#140F26;margin-left: 4px'>-->
<!--                        ¥-->
                      <span class='rmb-money'></span>
                      <span  style='font-size: 18px;font-weight: bolder;color: rgb(20, 15, 38);padding-top: -29px;float: right;position: absolute;margin-top: 3px;margin-left: 10px;'>
                        {{ iegAmount(Math.trunc(model.invoiceAmountRmb), 'total') }}
                      </span>
                    </td>
                    <td style='width: 50%' class="lastCol">
<!--                      USD-->
<!--                      <span style='font-size: 16px;font-weight: bolder; color:#140F26;margin-left: 4px'>-->
<!--                        $-->
                      <span class='usd-money'></span>
                      <span  style='font-size: 18px;font-weight: bolder;color: rgb(20, 15, 38);padding-top: -29px;float: right;position: absolute;margin-top: 3px;margin-left: 10px;'>
                        {{ iegAmount(Math.trunc(model.invoiceAmountUsd), 'total') }}
                      </span>
                    </td>
                  </tr>
                  <tr class="lastrow">
                    <td style='width: 50%'>
<!--                      EUR-->
<!--                      <span style='font-size: 16px;font-weight: bolder; color:#140F26;margin-left: 5px'>-->
<!--                        €-->
                      <span class='eur-money'></span>
                      <span  style='font-size: 18px;font-weight: bolder;color: rgb(20, 15, 38);padding-top: -29px;float: right;position: absolute;margin-top: 3px;margin-left: 10px;'>
                        {{ iegAmount(Math.trunc(model.invoiceAmountEur), 'total') }}
                      </span>
                    </td>
                    <td style='width: 50%' class="lastCol">
<!--                      JPY-->
<!--                      <span style='font-size: 16px;font-weight: bolder; color:#140F26;margin-left: 8px'>-->
<!--                        ￥-->
                      <span class='jpy-money'></span>
                      <span  style='font-size: 18px;font-weight: bolder;color: rgb(20, 15, 38);padding-top: -29px;float: right;position: absolute;margin-top: 3px;margin-left: 10px;'>
                        {{ iegAmount(Math.trunc(model.invoiceAmountJpy), 'total') }}
                      </span>
                    </td>
                  </tr>
                </table>
              </div>
            </div>
          </a-card>
        </a-col>
    </a-col>
		</a-row>
		<a-row :gutter="24">
			<a-col :sm="24" :md="17" :xl="17" style="padding-left:0px;padding-right: 0px;">
				<a-card class="card-main-index" style="height: 470px!important;width:100%;">
          <div style="float:left;width:50%;height:32px;margin-top: -10px;margin-bottom: 8px">
								<span class='font-title'>设备到厂进度</span>
          </div>
					<a-table ref="table" size="middle" rowKey="id" :style="{width:'100%'}" style='margin-top: 0px !important;'
						:scroll="{x:false,y:350}" :columns="columns1" :dataSource="dataSource1" :customRow="customRow"
						:pagination="ipagination" :loading="loading" @change="handleTableChange">
					</a-table>
				</a-card>
			</a-col>
      <a-col :sm="24" :md="7" :xl="7">
        <a-card style="height:470px!important">
          <div class="right-content green-money">
            <div>
              <div class="cont-data">快捷入口</div>
              <a-row style="margin-top:35px;">
                <a-col :span="8" style="text-align: center;">
                  <router-link :to="`/srm/bidding`">
                    <a-card :bordered="false" style="width: 100%;height:60px;">
                      <img slot="cover" alt="投标管理" src="~@/assets/diagram_images/inquiry_manage.svg"
                           height="29px" width="27px" />
                      <p style="text-align: center;margin-top:6px;">投标管理</p>
                    </a-card>
                  </router-link>
                </a-col>
                <a-col :span="8" style="text-align: center;">
                  <router-link :to="`/srm/inquiry`">
                    <a-card :bordered="false" style="width: 100%;height:60px">
                      <img slot="cover" alt="询价管理" src="~@/assets/diagram_images/inquiry_manage.svg"
                           height="29px" width="27px" />
                      <p style="text-align: center;margin-top:6px;">询价管理</p>
                    </a-card>
                  </router-link>
                </a-col>
                <a-col :span="8" style="text-align: center;">
                  <router-link :to="`/srm/contract`">
                    <a-card :bordered="false" style="width: 100%;height:100px">
                      <img slot="cover" alt="合同管理" src="~@/assets/diagram_images/bidding_manage.svg"
                           height="29px" width="27px" />
                      <p style="text-align: center;margin-top:6px;">合同管理</p>
                    </a-card>
                  </router-link>
                </a-col>

                <a-col :span="8" style="text-align: center;">
                  <router-link :to="`/srm/pay`">
                    <a-card :bordered="false" style="width: 100%;height:60px;">
                      <img slot="cover" alt="收款管理" src="~@/assets/diagram_images/payment_manage.svg"
                           height="29px" width="27px" />
                      <p style="text-align: center;margin-top:6px;">收款管理</p>
                    </a-card>
                  </router-link>
                </a-col>
                <a-col :span="8" style="text-align: center;">
                  <router-link :to="`/srm/invoice`">
                    <a-card :bordered="false" style="width: 100%;height:60px">
                      <img slot="cover" alt="发票管理" src="~@/assets/diagram_images/invoice_manage.svg"
                           height="29px" width="27px" />
                      <p style="text-align: center;margin-top:6px;">发票管理</p>
                    </a-card>
                  </router-link>
                </a-col>
              </a-row>
            </div>
            <div style="margin-top:35px;">
              <div class="cont-data">最近访问</div>
              <a-row style="margin-top:35px;">
                <a-col :span='8' style="text-align: center;" v-if="menuList != null && menuList.length > 0" v-for="(item,index) in menuList">
                  <router-link :to="item.url">
                    <a-card :bordered="false" style="width: 100%;height:60px" >
                      <img slot="cover" :alt="item.name" src="~@/assets/diagram_images/contract.svg"
                           height="29px" width="27px"/>
                      <p style="text-align: center;margin-top:6px;">{{item.name}}</p>
                    </a-card>
                  </router-link>
                </a-col>
              </a-row>
            </div>
          </div>
        </a-card>
      </a-col>
		</a-row>
	</div>
</template>

<script>
/* import Vue from 'vue'
import echarts from 'echarts' */
import { getAction } from '@api/manage'
import {
  iegAmount, isNullOrEmpty,isNotNullOrEmpty
} from '@/utils/util'
export default {
		name: "IndexChart",
		components: {

		},
		data() {
			return {
        isNotNullOrEmpty,
        isNullOrEmpty,
        model:{
          contractAmountRmb:0,
          contractAmountUsd:0,
          contractAmountEur:0,
          contractAmountJpy:0,
          payAmountRmb:0,
          payAmountUsd:0,
          payAmountEur:0,
          payAmountJpy:0,
          invoiceAmountRmb:0,
          invoiceAmountUsd:0,
          invoiceAmountEur:0,
          invoiceAmountJpy:0,
        },
        iegAmount,
			  dictCode:"contract_base,contract_name,id,contract_status = 4 and contract_second_party_id = '"+ this.$store.getters.userInfo.supplierId + "'",
        menuList:[],
        ipagination: {
          current: 1,
          pageSize: 10,
          pageSizeOptions: ['10', '20', '30'],
          showTotal: (total, range) => {
            return range[0] + '-' + range[1] + ' 共' + total + '条'
          },
          total: 0
        },
        dataSource1:[],
        suppId:'',
        queryParam:{},
			  contract:{},
			  bidding:{},
				loading: true,
				center: null,
				columns: [{
						title: '合同名称',
						dataIndex: 'contractName',
						width: '50%',
						scopedSlots: {
							customRender: 'contractName'
						},
					},
					{
						title: '进度',
						dataIndex: 'payProcess',
						width: '50%',
						scopedSlots: {
							customRender: 'payProcess'
						},
					}
				],
				columns1: [
				  {
						title: '序号',
						dataIndex: '',
						key: 'rowIndex',
						width: 100,
						align: "center",
						customRender: function(t, r, index) {
							return parseInt(index) + 1;
						}
					},
					{
						title: '设备名称',
						dataIndex: 'prodName',
						width: 160,
						scopedSlots: {
							customRender: 'prodName'
						},
					},
					{
						title: '需求数量',
						dataIndex: 'requireQty',
						width: 120,
						scopedSlots: {
							customRender: 'requireQty'
						},
					},
					{
						title: '在途数量',
						dataIndex: 'ongoingQty',
						width: 120,
						scopedSlots: {
							customRender: 'ongoingQty'
						},
					},
					{
						title: '到场数量',
						dataIndex: 'arrivedQty',
						width: 120,
						scopedSlots: {
							customRender: 'arrivedQty'
						},
					}
				],
				dataSource: []
			}
		},
		created() {
			setTimeout(() => {
				this.loading = !this.loading
			}, 1000)
      this.fetchBiddingList();
			this.fetchContractList();
			this.fetchPayList();
      this.fetchEqpList();
      this.fetchHistory();
      this.fetchAmount();
			this.suppId = this.$store.getters.userInfo.supplierId;
		},
		mounted() {

		},
		methods: {
      fetchAmount(){
        this.model = {
          contractAmountRmb:0,
          contractAmountUsd:0,
          contractAmountEur:0,
          contractAmountJpy:0,
          payAmountRmb:0,
          payAmountUsd:0,
          payAmountEur:0,
          payAmountJpy:0,
          invoiceAmountRmb:0,
          invoiceAmountUsd:0,
          invoiceAmountEur:0,
          invoiceAmountJpy:0,
        };
        let url = "/srm/contractBase/fetchAmount";
        getAction(url,{}).then(res => {
          if(res.success){
            this.model = res.result;
          }
        })
      },
      fetchHistory(){
        let url = "/srm/clickMenuHistory/list";
        getAction(url,{}).then(res => {
          this.menuList = res.result;
        })
      },
      handleTableChange(pagination, filters, sorter) {
        if (Object.keys(sorter).length > 0) {
          this.isorter.column = sorter.field;
          this.isorter.order = 'ascend' === sorter.order ? 'asc' : 'desc';
        }
        this.ipagination = pagination;
        this.fetchEqpList();
      },
      fetchEqpList(){
        let url = "/srm/contractBase/fetchEqpList";
        getAction(url,{code:this.queryParam.prodCode}).then(res => {
          this.dataSource1 = res.result;
        })
      },
      fetchPayList(){
        let url = "/srm/contractBase/fetchPayList";
        getAction(url,{id:this.queryParam.id}).then(res => {
          this.dataSource = res.result;
        })
      },
		  fetchContractList(){
        let url = "/srm/contractBase/fetchContractList";
        getAction(url,{}).then(res => {
          this.contract = res.result;
        })
      },
		  fetchBiddingList(){
		    let url = "/srm/contractBase/fetchBiddingList";
        getAction(url,{}).then(res => {
          this.bidding = res.result;
        })
      },
			customRow(record, index) {
				return {
					// 这个style就是我自定义的属性，也就是官方文档中的props
					style: {
						// 行背景色
						// 字体类型
						'line-height': '20px',
						'padding-top': '0px',
						'padding-bottom': '0px',
						'border-bottom': '0px',
					},
				}
			},
		}
	}
</script>
<style>
	.point-div {
		line-height: 50px;
	}

	.orange-point::before {
		content: " ";
		width: 8px;
		height: 8px;
		background: orange;
		border-radius: 4px;
		display: inline-block;
	}

	.bule-point::before {
		content: " ";
		width: 8px;
		height: 8px;
		background: #3662EC;
		border-radius: 4px;
		display: inline-block;
	}

	.gray-point::before {
		content: " ";
		width: 8px;
		height: 8px;
		background: #D8D8D8;
		border-radius: 4px;
		display: inline-block;
	}

	/* #E8EAF2 */
</style>
<style lang="less" scoped>
	/*  .card-main-index{
	  height: 240px!important;
  } */
	.circle-cust {
		position: relative;
		top: 28px;
		left: -100%;
	}

	.extra-wrapper {
		line-height: 55px;
		padding-right: 24px;

		.extra-item {
			display: inline-block;
			margin-right: 24px;

			a {
				margin-left: 24px;
			}
		}
	}

	/* 首页访问量统计 */
	.head-info {
		position: relative;
		text-align: left;
		padding: 0 32px 0 0;
		min-width: 125px;

		&.center {
			text-align: center;
			padding: 0 32px;
		}

		span {
			color: rgba(0, 0, 0, .45);
			display: inline-block;
			font-size: .95rem;
			line-height: 42px;
			margin-bottom: 4px;
		}

		p {
			line-height: 42px;
			margin: 0;

			a {
				font-weight: 600;
				font-size: 1rem;
			}
		}
	}
</style>
<style scoped>
	.ant-table-tbody tr td {
		border-bottom: 0px !important;
		line-height: 20px;
	}

	.project-select {
		width: 220px;
		height: 30px;
		background: #E8EAF2;
		border: 0px;
		border-radius: 36px 36px 36px 36px;
		opacity: 1
	}

	.bidding-rate {
		height: 160px;
		padding-top: 0px;
		background: #FF8F1F;
		border-radius: 0px 0px 0px 0px;
		opacity: 0.7;
		line-height: 110px;
	}

	.excute-rate {
		padding-top: 0px;
		height: 160px;
		background: #3662EC;
		border-radius: 0px 0px 0px 0px;
		opacity: 0.7;
		line-height: 110px;
	}

	.bidding-rate-bottom {
		height: 50px;
		position: absolute;
		margin-top: -50px;
		background: #FFFFFF;
		border-radius: 0px 0px 0px 0px;
		opacity: 0.7;
		display: -webkit-inline-box;
		white-space: nowrap;
		width: 100%;
		line-height: 50px;
	}

	.excute-rate-bottom {
		height: 50px;
		position: absolute;
		margin-top: -50px;
		background: #FFFFFF;
		border-radius: 0px 0px 0px 0px;
		opacity: 0.7;
		width: 100%;
		line-height: 50px;
		display: -webkit-inline-box;
		white-space: nowrap;
	}

	.contract-circle {
		width: 30px;
		height: 30px;
		opacity: 0.2;
		color: ;
		background-color: #6495ED;
		border-radius: 50%;
		text-align: center;
		line-height: 30px;
	}
  /*table{*/
  /*  border-collapse: collapse;*/
  /*}*/
  /*table,table tr td {*/
  /*  border:1px solid #ccc;*/
  /*}*/
  /*table tr td{*/
  /*  padding: 5px 10px;*/
  /*}*/
  table{
    border-collapse: collapse;
    border: 0px solid #dddddd;
  }

  table tr td {
    border-top: 0;
    border-right: 1px solid #dddddd;
    border-bottom: 1px solid #dddddd;
    border-left: 0;
    padding: 10px 10px;
  }

  table tr.lastrow td {
    border-bottom: 0;
  }

  table tr td.lastCol {
    border-right: 0;
  }


</style>
