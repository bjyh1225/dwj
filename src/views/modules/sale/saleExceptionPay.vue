<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>支付异常列表</h4>
		</div>
		<div class="tool-header">
			<el-form :inline="true" @keyup.enter.native="getDataList()">
				<el-form-item>
					<el-select v-model="type" size="mini" clearable placeholder="隐患类型">
						<el-option label="订单微信支付隐患" value="0"></el-option>
						<el-option label="订单零钱支付隐患" value="1"></el-option>
						<el-option label="会员费支付隐患" value="2"></el-option>
						<el-option label="商家签约费支付隐患" value="3"></el-option>
						<el-option label="零钱充值隐患" value="4"></el-option>
						<el-option label="零钱提现隐患" value="5"></el-option>
						<el-option label="商家提现隐患" value="6"></el-option>
						<el-option label="订单退款隐患" value="7"></el-option>
					</el-select>
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
			<el-table-column prop="tradeNo" header-align="center" align="center" label="单号">
			</el-table-column>
			<el-table-column prop="amount" header-align="center" align="center" label="金额">
			</el-table-column>
			<el-table-column prop="sellerCode" header-align="center" align="center" label="商家编码">
			</el-table-column>
			<el-table-column prop="userId" header-align="center" align="center" label="用户编码">
			</el-table-column>
			<el-table-column prop="openid" header-align="center" align="center" label="openId">
			</el-table-column>
			<el-table-column prop="orderCode" header-align="center" align="center" label="订单编码">
			</el-table-column>
			<el-table-column prop="cardCode" header-align="center" align="center" label="卡券编码">
			</el-table-column>
		</el-table>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				dataList: [],
				type: '',
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
					type: ''
				}
			},
			// 获取数据列表
			getDataList() {
				this.dataListLoading = true;
				this.$http({
					url: this.$http.adornUrl("/exception/pay/findList"),
					method: "post",
					data: this.$http.adornData({
						type: this.type
					})
				}).then(({
					data
				}) => {
					if(data && data.code === 0) {
						this.dataList = data.data;
					} else {
						this.dataList = [];
					}
					this.dataListLoading = false;
				});
			},
			indexMethod(index) {
				return(this.currentPage - 1) * this.pageSize + index + 1
			},
			// 多选
			selectionChangeHandle(val) {
				this.dataListSelections = val;
			},
		}
	};
</script>

<style scoped>

</style>