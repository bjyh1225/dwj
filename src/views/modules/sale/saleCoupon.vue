<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>优惠券管理</h4>
		</div>
		<div class="tool-header">
			<el-form :inline="true" :model="condition" @keyup.enter.native="getDataList()">
				<el-form-item>
					<el-select size="mini" v-model="condition.industryTypeCode" placeholder="行业类别" clearable class="w-100">
						<el-option v-for="item in types" :key="item.value" :label="item.label" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.createSubjectType" size="mini" clearable placeholder="发布主体">
						<el-option label="平台" value="0"></el-option>
						<el-option label="商家" value="1"></el-option>
						<el-option label="消费者" value="2"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.paySubjectType" size="mini" clearable placeholder="支付主体">
						<el-option label="平台" value="0"></el-option>
						<el-option label="商家" value="1"></el-option>
						<el-option label="消费者" value="2"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.sellerCode" clearable placeholder="商家名称" clearable class="w-100">
						<el-option v-for="item in allsellers" :key="item.value" :label="item.name" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.batchName" placeholder="批次名称" size="mini" class="w120"></el-input>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.publishType" size="mini" clearable placeholder="发放方式">
						<el-option label="线上" value="0"></el-option>
						<el-option label="线下" value="1"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.reflectStatus" size="mini" clearable placeholder="提现状态">
						<el-option label="不能提现" value="0"></el-option>
						<el-option label="可提现" value="1"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.effectStatus" size="mini" clearable placeholder="有效状态">
						<el-option label="有效" value="0"></el-option>
						<el-option label="无效" value="1"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.status" size="mini" clearable placeholder="状态">
						<el-option label="启用" value="0"></el-option>
						<el-option label="禁用" value="1"></el-option>
					</el-select>
				</el-form-item>

				<el-form-item>
					<el-select v-model="condition.recivetStatus" size="mini" clearable placeholder="领取状态">
						<el-option label="未领取" value="0"></el-option>
						<el-option label="已领取" value="1"></el-option>
					</el-select>
				</el-form-item>

				<el-form-item>
					<el-select v-model="condition.useStatus" size="mini" clearable placeholder="使用状态">
						<el-option label="未使用" value="0"></el-option>
						<el-option label="已使用" value="1"></el-option>
					</el-select>
				</el-form-item>

				<el-form-item>
					<el-select v-model="condition.useEffectStatus" size="mini" clearable placeholder="使用有效状态">
						<el-option label="有效" value="0"></el-option>
						<el-option label="无效" value="1"></el-option>
					</el-select>
				</el-form-item>

				<el-form-item>
					<el-date-picker v-model="condition.reciveDate" type="date" placeholder="领取日期">
					</el-date-picker>
				</el-form-item>

				<el-form-item>
					<el-date-picker v-model="condition.useOrderDate" type="date" placeholder="订单日期">
					</el-date-picker>
				</el-form-item>

				<el-form-item>
					<el-button @click="searchHndel()" size="mini" type="primary">查询</el-button>
				</el-form-item>
				<el-form-item>
					<el-button @click="searchReset" size="mini" type="primary">重置</el-button>
				</el-form-item>
			</el-form>
		</div>
		<el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle" ref="multipleTable" style="width: 100%;">
			<el-table-column type="selection" header-align="center" align="center" width="50">
			</el-table-column>
			<el-table-column prop="industryTypeCode" header-align="center" align="center" label="行业类别">
			</el-table-column>
			<el-table-column prop="createSubjectType" header-align="center" align="center" label="发放主体">
			</el-table-column>
			<el-table-column prop="paySubjectType" header-align="center" align="center" label="支付主体">
			</el-table-column>
			<el-table-column prop="sellerCode" header-align="center" align="center" label="商家编码">
			</el-table-column>
			<el-table-column prop="fullName" header-align="center" align="center" label="商家名称">
			</el-table-column>
			<el-table-column prop="batchCode" header-align="center" align="center" label="批次编码">
			</el-table-column>
			<el-table-column prop="batchName" header-align="center" align="center" label="批次名称">
			</el-table-column>
			<el-table-column prop="deductionAmount" header-align="center" align="center" label="抵扣金额">
			</el-table-column>
			<el-table-column prop="limitLowerAmount" header-align="center" align="center" label="最低限额">
			</el-table-column>
			<el-table-column prop="limitTimeStart" header-align="center" align="center" label="开始时间">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.limitTimeStart).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.limitTimeStart).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="limitTimeEnd" header-align="center" align="center" label="截止时间">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.limitTimeEnd).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.limitTimeEnd).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="batchSize" header-align="center" align="center" label="发放数量">
			</el-table-column>
			<el-table-column prop="realSize" header-align="center" align="center" label="实时数量">
			</el-table-column>
			<el-table-column prop="publishType" header-align="center" align="center" label="发放方式">
			</el-table-column>
			<el-table-column prop="reflectStatus" header-align="center" align="center" label="提现状态">
				<template slot-scope="scope">
					<span v-if="scope.row.reflectStatus == 0">不能提现</span>
					<span v-else-if="scope.row.reflectStatus == 1">可提现</span>
				</template>
			</el-table-column>
			<el-table-column prop="effectStatus" header-align="center" align="center" label="有效状态">
				<template slot-scope="scope">
					<span v-if="scope.row.effectStatus == 0">有效</span>
					<span v-else-if="scope.row.effectStatus == 1">无效</span>
				</template>
			</el-table-column>
			<el-table-column prop="couponCode" header-align="center" align="center" label="优惠券编码">
			</el-table-column>
			<el-table-column prop="recivetStatus" header-align="center" align="center" label="领取状态">
				<template slot-scope="scope">
					<span v-if="scope.row.recivetStatus == 0">未领取</span>
					<span v-else-if="scope.row.recivetStatus == 1">已领取</span>
				</template>
			</el-table-column>
			<el-table-column prop="useStatus" header-align="center" align="center" label="使用状态">
				<template slot-scope="scope">
					<span v-if="scope.row.useStatus == 0">未使用</span>
					<span v-else-if="scope.row.useStatus == 1">已使用</span>
				</template>
			</el-table-column>
			<el-table-column prop="useEffectStatus" header-align="center" align="center" label="使用有效状态">
				<template slot-scope="scope">
					<span v-if="scope.row.useEffectStatus == 0">有效</span>
					<span v-else-if="scope.row.useEffectStatus == 1">无效</span>
				</template>
			</el-table-column>
			<el-table-column prop="reciveDate" header-align="center" align="center" label="领取日期">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.reciveDate).toLocaleDateString().replace(/\//g, "-")}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="useBuyerCode" header-align="center" align="center" label="使用消费者编码">
			</el-table-column>
			<el-table-column prop="useBuyerName" header-align="center" align="center" label="使用消费者名称">
			</el-table-column>
			<el-table-column prop="useShortName" header-align="center" align="center" label="使用商家名称">
			</el-table-column>
			<el-table-column prop="useSellerCode" header-align="center" align="center" label="使用商家编码">
			</el-table-column>
			<el-table-column prop="useOrderCode" header-align="center" align="center" label="使用订单编码">
			</el-table-column>
			<el-table-column prop="useOrderDate" header-align="center" align="center" label="使用订单日期">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.useOrderDate).toLocaleDateString().replace(/\//g, "-")}}</span>
				</template>
			</el-table-column>
		</el-table>
		<el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex" :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage" layout="total, sizes, prev, pager, next, jumper"></el-pagination>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				dataList: [],
				condition: {
					industryTypeCode: '',
					createSubjectType: '',
					paySubjectType: '',
					sellerCode: '',
					batchName: '',
					publishType: '',
					reflectStatus: '',
					effectStatus: '',
					status: '',
					recivetStatus: '',
					useStatus: '',
					useEffectStatus: '',
					reciveDate: '',
					useOrderDate: '',
					page: 1,
					size: 10
				},
				types: [],
				allsellers: [],
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false,
			};
		},
		activated() {
			this.getDataList()
			this.getTypes()
			this.showSeller()
		},
		methods: {
			searchHndel() {
				this.pageIndex = 1
				this.pageSize = 10
				this.getDataList()
			},
			searchReset() {
				this.condition =  {
					industryTypeCode: '',
					createSubjectType: '',
					paySubjectType: '',
					sellerCode: '',
					batchName: '',
					publishType: '',
					reflectStatus: '',
					effectStatus: '',
					status: '',
					recivetStatus: '',
					useStatus: '',
					useEffectStatus: '',
					reciveDate: '',
					useOrderDate: '',
					page: 1,
					size: 10
				}
			},
			//显示所有商家
			showSeller() {
				this.$http({
					url: this.$http.adornUrl('/sale/saleSeller/querySaleSeller'),
					method: "post",
					params: this.$http.adornParams()
				}).then(({
					data
				}) => {
					this.allsellers = data.data
				});
			},
			getTypes() {
				this.$http({
					url: this.$http.adornUrl("/sale/saleDict/findByParentCode"),
					method: "get",
					params: this.$http.adornParams({
						parentCode: 'industry'
					})
				}).then(({
					data
				}) => {
					this.types = data.dataList
				});
			},
			// 获取数据列表
			getDataList() {
				this.dataListLoading = true;
				this.condition.page = this.pageIndex
				this.condition.size = this.pageSize
				this.$http({
					url: this.$http.adornUrl("/sale/saleCoupon/list"),
					method: "post",
					data: this.$http.adornData({
						page: this.pageIndex,
						size: this.pageSize,
						condition: this.condition
					})
				}).then(({
					data
				}) => {
					if(data && data.code === 0) {
						this.dataList = data.page.list;
						this.totalPage = data.page.totalCount;
					} else {
						this.dataList = [];
						this.totalPage = 0;
					}
					this.dataListLoading = false;
				});
			},
			indexMethod(index) {
				return(this.currentPage - 1) * this.pageSize + index + 1
			},

			// 每页数
			sizeChangeHandle(val) {
				this.pageSize = val;
				this.pageIndex = 1;
				this.getDataList();
			},
			// 当前页
			currentChangeHandle(val) {
				this.pageIndex = val;
				this.getDataList();
			},
		}
	};
</script>

<style scoped>

</style>