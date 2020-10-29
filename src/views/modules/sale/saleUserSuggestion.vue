<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>意见反馈</h4>
		</div>
		<div class="tool-header">
			<el-form :inline="true" :model="condition" @keyup.enter.native="getDataList()">
				<el-form-item>
					<el-select v-model="condition.suggestLable" size="mini" clearable placeholder="意见标题">
						<el-option v-for="item in titls" :key="item.value" :label="item.label" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.suggestSource" size="mini" clearable placeholder="意见来源">
						<el-option label="小程序会员端" value="0"></el-option>
						<el-option label="小程序商家端" value="1"></el-option>
						<el-option label="app会员端" value="2"></el-option>
						<el-option label="app商家端" value="3"></el-option>
						<el-option label="pc会员端" value="4"></el-option>
						<el-option label="pc商家端" value="5"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.suggestNote" placeholder="意见描述" size="mini" class="w120"></el-input>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.replyStatus" size="mini" clearable placeholder="回复状态">
						<el-option label="未回复" value="0"></el-option>
						<el-option label="已回复" value="1"></el-option>
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
			<el-table-column prop="suggestSource" header-align="center" align="center" label="意见来源">
				<template slot-scope="scope">
					<span v-if="scope.row.suggestSource == 0">小程序会员端</span>
					<span v-else-if="scope.row.suggestSource == 1">小程序商家端</span>
					<span v-else-if="scope.row.suggestSource == 2">app会员端</span>
					<span v-else-if="scope.row.suggestSource == 3">app商家端</span>
					<span v-else-if="scope.row.suggestSource == 4">pc会员端</span>
					<span v-else-if="scope.row.suggestSource == 5">pc商家端</span>
				</template>
			</el-table-column>
			<el-table-column prop="suggestLable" header-align="center" align="center" label="意见标题">
			</el-table-column>
			<el-table-column prop="suggestCode" header-align="center" align="center" label="意见编码">
			</el-table-column>
			<el-table-column prop="suggestNote" header-align="center" align="center" label="描述">
			</el-table-column>
			<el-table-column prop="replyStatus" header-align="center" align="center" label="回复状态">
				<template slot-scope="scope">
					<span v-if="scope.row.replyStatus == 0">未回复</span>
					<span v-else-if="scope.row.replyStatus == 1">已回复</span>
				</template>
			</el-table-column>
			<el-table-column prop="replyNote" header-align="center" align="center" label="回复意见">
			</el-table-column>
			<el-table-column prop="suggestPictureFirst" header-align="center" align="center" label="图片1">
				<template slot-scope="scope">
					<img :src="scope.row.suggestPictureFirst" alt="" style="width: 100px;display: block;margin: 0 auto;">
				</template>
			</el-table-column>
			<!--<el-table-column prop="suggestPictureSecond" header-align="center" align="center" label="图片2">
				<template slot-scope="scope">
					<img :src="scope.row.suggestPictureSecond" alt="" style="width: 100px;display: block;margin: 0 auto;">
				</template>
			</el-table-column>
			<el-table-column prop="suggestPictureThird " header-align="center" align="center" label="图片3">
				<template slot-scope="scope">
					<img :src="scope.row.suggestPictureThird" alt="" style="width: 100px;display: block;margin: 0 auto;">
				</template>
			</el-table-column>
			<el-table-column prop="suggestPictureFourth" header-align="center" align="center" label="图片4">
				<template slot-scope="scope">
					<img :src="scope.row.suggestPictureFourth" alt="" style="width: 100px;display: block;margin: 0 auto;">
				</template>
			</el-table-column>-->
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
					suggestLable: '',
					suggestSource: '',
					suggestNote: '',
					replyStatus: '',
					page: 1,
					size: 10
				},
				titls: [],
				allsellers: [],
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false
			};
		},
		activated() {
			this.getTitls()
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
					suggestLable: '',
					suggestSource: '',
					suggestNote: '',
					replyStatus: '',
					page: 1,
					size: 10
				}
			},
			getTitls() {
				this.$http({
					url: this.$http.adornUrl("/sale/saleDict/findByParentCode"),
					method: "get",
					params: this.$http.adornParams({
						parentCode:'suggestion'
					})
				}).then(({
					data
				}) => {
					this.titls = data.dataList
				});
			},
			
			// 获取数据列表
			getDataList() {
				this.dataListLoading = true;
				this.condition.page = this.pageIndex
				this.condition.size = this.pageSize
				this.$http({
					url: this.$http.adornUrl("/sale/saleUserSuggestion/list"),
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