<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>合同管理</h4>
		</div>
		<div class="tool-header">
			<el-form :inline="true" :model="condition" @keyup.enter.native="getDataList()">
				<el-form-item>
					<el-select v-model="condition.contractStatus" size="mini" clearable placeholder="合同有效状态">
						<el-option label="有效" value="0"></el-option>
						<el-option label="无效" value="1"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.contractSignStatus" size="mini" clearable placeholder="合同签署状态">
						<el-option label="未签署" value="0"></el-option>
						<el-option label="已签署" value="1"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.sellerName" placeholder="商家名称" size="mini" class="w120"></el-input>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.sellerCode" placeholder="商家编码" size="mini" class="w120"></el-input>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.contractId" placeholder="合同编码" size="mini" class="w120"></el-input>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.buyerCode" placeholder="消费者编码" size="mini" class="w120"></el-input>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.nickName" placeholder="消费者昵称" size="mini" class="w120"></el-input>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.personMobile" placeholder="个人手机号" size="mini" class="w120"></el-input>
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
			<el-table-column prop="sellerName" header-align="center" align="center" label="商家名称">
			</el-table-column>
			<el-table-column prop="sellerCode" header-align="center" align="center" label="商家编码">
			</el-table-column>
			<el-table-column prop="contractId" header-align="center" align="center" label="合同编码">
			</el-table-column>
			<el-table-column prop="contractStatus" header-align="center" align="center" label="合同有效状态">
				<template slot-scope="scope">
					<span v-if="scope.row.contractStatus == 0">有效</span>
					<span v-else>无效</span>
				</template>
			</el-table-column>
			<el-table-column prop="contractSignStatus" header-align="center" align="center" label="合同签署状态">
				<template slot-scope="scope">
					<span v-if="scope.row.contractSignStatus == 0">未签署</span>
					<span v-else>已签署</span>
				</template>
			</el-table-column>
			<el-table-column prop="personName" header-align="center" align="center" label="个人姓名">
			</el-table-column>
			<el-table-column prop="personMobile" header-align="center" align="center" label="个人手机号">
			</el-table-column>
			<el-table-column prop="userType" header-align="center" align="center" label="用户身份">
				<template slot-scope="scope">
					<span v-if="scope.row.userType == 0">法人</span>
					<span v-else-if="scope.row.userType == 1">店长</span>
					<span v-else>财务</span>
				</template>
			</el-table-column>
			<el-table-column prop="buyerCode" header-align="center" align="center" label="消费者编码">
			</el-table-column>
			<el-table-column prop="nickName" header-align="center" align="center" label="消费者昵称">
			</el-table-column>
			<el-table-column prop="" header-align="center" align="center" label="操作">
				<template slot-scope="scope">
					<el-button type="danger" size="mini" @click="delhandel(scope.row.id)">作废</el-button>
					<el-button type="primary" size="mini" @click="loadhandel(scope.row.id)" style="margin: 0;margin-top: 10px;">下载</el-button>
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
					contractStatus: '',
					contractSignStatus: '',
					sellerName: '',
					sellerCode: '',
					contractId: '',
					buyerCode: '',
					nickName: '',
					personMobile: '',
					page: 1,
					size: 10
				},
				types: [],
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false,
				refiseVisible: false,
				note: '',
				curid: ''
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
					contractStatus: '',
					contractSignStatus: '',
					sellerName: '',
					sellerCode: '',
					contractId: '',
					buyerCode: '',
					nickName: '',
					personMobile: '',
					page: 1,
					size: 10
				}
			},
			// 获取数据列表
			getDataList() {
				this.dataListLoading = true;
				this.condition.page = this.pageIndex
				this.condition.size = this.pageSize
				this.$http({
					url: this.$http.adornUrl("/sale/saleContract/list"),
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
			},
			delhandel(id) {
				this.$confirm(`确定对当前行进行操作?`, "提示", {
						confirmButtonText: "确定",
						cancelButtonText: "取消",
						type: "warning"
					})
					.then(() => {
						var formData = new FormData();
						formData.append("id", id);
						this.$http.post(this.$http.adornUrl("/sale/saleContract/nullify"), formData)
							.then(({
								data
							}) => {
								if(data && data.code === 0) {
									this.$message({
										message: "操作成功",
										type: "success",
										duration: 1500,
										onClose: () => {
											this.getDataList();
										}
									});
								} else {
									this.$message.error(data.msg);
								}
							});
					})
					.catch(() => {});

			},
			loadhandel(id) {
				let token = this.$cookie.get('token')
				window.open(this.$http.adornUrl("/sale/saleContract/downContract/" + id + "?token=" + token))
			}
		}
	};
</script>

<style scoped>

</style>