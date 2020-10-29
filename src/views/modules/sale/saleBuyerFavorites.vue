<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>收藏商家</h4>
		</div>
		<div class="tool-header">
			<el-form :inline="true" :model="condition" @keyup.enter.native="getDataList()">
				<el-form-item>
					<el-select size="mini" v-model="condition.industryTypeCode" placeholder="行业类别" clearable class="w-100">
						<el-option v-for="(item,index) in types" :key="index" :label="item.label" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.sellerCode" size="mini" clearable placeholder="商家名称" clearable class="w-100">
						<el-option v-for="(item,index) in allsellers" :key="index" :label="item.name" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-button @click="searchHndel()" type="primary" size="mini">查询</el-button>
				</el-form-item>
				<el-form-item>
					<el-button @click="searchReset" size="mini" type="primary">重置</el-button>
				</el-form-item>
			</el-form>
		</div>
		<el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle" ref="multipleTable" style="width: 100%;">
			<el-table-column type="selection" header-align="center" align="center" width="50">
			</el-table-column>
			<el-table-column prop="industryTypeName" header-align="center" align="center" label="行业类别">
			</el-table-column>
			<el-table-column prop="sellerCode" header-align="center" align="center" label="商家编码">
			</el-table-column>
			<el-table-column prop="fullName" header-align="center" align="center" label="商家名称">
			</el-table-column>
			<el-table-column prop="favoritesNum" header-align="center" align="center" label="数量">
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
					sellerCode: '',
					page: 1,
					size: 10
				},
				types: [],
				allsellers: [],
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false
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
			// 获取数据列表
			getDataList() {
				this.dataListLoading = true;
				this.condition.page = this.pageIndex
				this.condition.size = this.pageSize
				this.$http({
					url: this.$http.adornUrl("/sale/saleBuyerFavorites/list"),
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