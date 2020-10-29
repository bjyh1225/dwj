<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>零钱支付记录</h4>
		</div>
		<div class="tool-header">
			<el-form :inline="true" :model="condition" @keyup.enter.native="getDataList()">
				<el-form-item>
					<el-input v-model="condition.payCode" placeholder="支付单号" size="mini" class="w120"></el-input>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.userId" placeholder="用户编码" size="mini" class="w120"></el-input>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.userName" placeholder="用户昵称" size="mini" class="w120"></el-input>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.callbackInterface" placeholder="回调接口" size="mini" class="w120"></el-input>
				</el-form-item>
				<el-form-item>
					<el-date-picker v-model="condition.PayTime" size="mini" type="daterange" value-format="yyyy-MM-dd" range-separator="至" start-placeholder="支付起始时间" end-placeholder="支付结束时间">
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
			<el-table-column prop="uuid" header-align="center" align="center" label="uuid">
			</el-table-column>
			<el-table-column prop="payCode" header-align="center" align="center" label="支付单号">
			</el-table-column>
			<el-table-column prop="userId" header-align="center" align="center" label="用户编码">
			</el-table-column>
			<el-table-column prop="userName" header-align="center" align="center" label="用户姓名">
			</el-table-column>
			<el-table-column prop="openId" header-align="center" align="center" label="openId">
			</el-table-column>
			<el-table-column prop="callbackInterface" header-align="center" align="center" label="回调接口">
			</el-table-column>
			<el-table-column prop="callbackParameter" header-align="center" align="center" label="回调参数">
			</el-table-column>
			<el-table-column prop="payAmount" header-align="center" align="center" label="支付金额">
			</el-table-column>
			<el-table-column prop="beforePayAmount" header-align="center" align="center" label="支付前金额">
			</el-table-column>
			<el-table-column prop="payTime" header-align="center" align="center" label="支付时间">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.payTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.payTime).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="payDelayTime" header-align="center" align="center" label="支付超时时间">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.payDelayTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.payDelayTime).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="createTime" header-align="center" align="center" label="创建时间">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.createTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.createTime).toTimeString().substr(0, 8)}}</span>
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
					payCode:'',
					userId:'',
					userName:'',
					callbackInterface:'',
					PayTime:'',
					payTimeStart:'',
					payTimeEnd:''
				},
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false
			};
		},
		activated() {
			this.getDataList()
		},
		methods: {
			searchHndel() {
				this.pageIndex = 1
				this.pageSize = 10
				this.getDataList()
			},
			searchReset() {
				this.condition = {
					payCode:'',
					userId:'',
					userName:'',
					callbackInterface:'',
					PayTime:'',
					payTimeStart:'',
					payTimeEnd:''
				}
			},
			// 获取数据列表
			getDataList() {
				this.dataListLoading = true;
				this.condition.payTimeStart = new Date(this.condition.PayTime[0]).getTime()
				this.condition.payTimeEnd = new Date(this.condition.PayTime[1]).getTime()
				this.condition.page = this.pageIndex
				this.condition.size = this.pageSize
				this.$http({
					url: this.$http.adornUrl("/sale/saleCashPayRecord/list"),
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