<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>财务记录</h4>
		</div>
		<div class="tool-header">
			<el-form :inline="true" :model="condition" @keyup.enter.native="getDataList()">
				<el-form-item>
					<el-select v-model="condition.recordType" size="mini" clearable placeholder="记录类型">
						<el-option label="会员费支出" value="0"></el-option>
						<el-option label="签约费支出" value="1"></el-option>
						<el-option label="会员费占比" value="2"></el-option>
						<el-option label="签约费占比" value="3"></el-option>
						<el-option label="平台留存" value="4"></el-option>
						<el-option label="会员提现" value="5"></el-option>
						<el-option label="商家提现" value="6"></el-option>
						<el-option label="零钱充值" value="7"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.incomeType" size="mini" clearable placeholder="占比类型">
						<el-option label="直推" value="0"></el-option>
						<el-option label="间推" value="1"></el-option>
						<el-option label="平台" value="2"></el-option>
						<el-option label="个人" value="3"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.userId" placeholder="用户编码" size="mini" class="w120"></el-input>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.sellerCode" placeholder="商家编码" size="mini" class="w120"></el-input>
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
			<el-table-column prop="recordType" header-align="center" align="center" label="记录类型">
				<template slot-scope="scope">
					<span v-if="scope.row.recordType == 0">会员费支出</span>
					<span v-else-if="scope.row.recordType == 1">签约费支出</span>
					<span v-else-if="scope.row.recordType == 2">会员费占比</span>
					<span v-else-if="scope.row.recordType == 3">签约费占比</span>
					<span v-else-if="scope.row.recordType == 4">平台留存</span>
					<span v-else-if="scope.row.recordType == 5">会员提现</span>
					<span v-else-if="scope.row.recordType == 6">商家提现</span>
					<span v-else-if="scope.row.recordType == 7">零钱充值</span>
				</template>
			</el-table-column>
			<el-table-column prop="amount" header-align="center" align="center" label="金额">
			</el-table-column>
			<el-table-column prop="incomeType" header-align="center" align="center" label="占比类型">
				<template slot-scope="scope">
					<span v-if="scope.row.incomeType == 0">直推</span>
					<span v-else-if="scope.row.incomeType == 1">间推</span>
					<span v-else-if="scope.row.incomeType == 2">平台</span>
					<span v-else-if="scope.row.incomeType == 3">个人</span>
				</template>
			</el-table-column>
			<el-table-column prop="openId" header-align="center" align="center" label="微信标识">
			</el-table-column>
			<el-table-column prop="userId" header-align="center" align="center" label="用户编码">
			</el-table-column>
			<el-table-column prop="userName" header-align="center" align="center" label="用户姓名">
			</el-table-column>
			<el-table-column prop="userCardId" header-align="center" align="center" label="身份证号">
			</el-table-column>
			<el-table-column prop="sellerCode" header-align="center" align="center" label="商家编码">
			</el-table-column>
			<el-table-column prop="sellerName" header-align="center" align="center" label="商家名称">
			</el-table-column>
			<el-table-column prop="startTime" header-align="center" align="center" label="开始时间">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.startTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.startTime).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="endTime" header-align="center" align="center" label="结束时间">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.endTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.endTime).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="note" header-align="center" align="center" label="备注">
			</el-table-column>
			<el-table-column prop="status" header-align="center" align="center" label="状态">
				<template slot-scope="scope">
					<span v-if="scope.row.status == 0">启用</span>
					<span v-else-if="scope.row.status == 1">禁用</span>
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
					recordType: '',
					incomeType: '',
					userId: '',
					sellerCode: '',
					page: 1,
					size: 10
				},
				types: [],
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false
			};
		},
		activated() {
			this.getDataList()
			this.getTypes()
		},
		methods: {
			searchHndel() {
				this.pageIndex = 1
				this.pageSize = 10
				this.getDataList()
			},
			searchReset() {
				this.condition = {
					recordType: '',
					incomeType: '',
					userId: '',
					sellerCode: '',
					page: 1,
					size: 10
				}
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
					url: this.$http.adornUrl("/sale/saleFinanceRecord/list"),
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
			// 多选
			selectionChangeHandle(val) {
				this.dataListSelections = val;
			}
		}
	};
</script>

<style scoped>

</style>