<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>会员提现管理</h4>
		</div>
		<div class="tool-header">
			<el-form :inline="true" :model="condition" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="condition.userName" placeholder="会员昵称" size="mini" class="w120"></el-input>
        </el-form-item>
				<el-form-item>
					<el-input v-model="condition.userId" placeholder="会员编码" size="mini" class="w120"></el-input>
				</el-form-item>
        <el-form-item>
          <el-select v-model="condition.cashProcessNodeStatus" size="mini" clearable placeholder="审核状态">
            <el-option label="待处理" value="0"></el-option>
            <el-option label="通过" value="2"></el-option>
            <el-option label="驳回" value="3"></el-option>
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
      <el-table-column prop="userName" header-align="center" align="center" width="150"  label="会员昵称">
      </el-table-column>
			<el-table-column prop="userId" header-align="center" align="center" label="会员编码" width="90">
			</el-table-column>
      <el-table-column prop="openId" header-align="center" align="center" label="OPENID" width="280">
      </el-table-column>
			<el-table-column prop="cashOutType" header-align="center" align="center" label="账户类型" width="80">
				<template slot-scope="scope">
					<span v-if="scope.row.cashOutType == 0">支付宝</span>
					<span v-else>微信</span>
				</template>
			</el-table-column>
			<el-table-column prop="historyAmount" header-align="center" align="center" label="账户余额" width="80">
			</el-table-column>
			<el-table-column prop="amount" header-align="center" align="center" label="提现金额" width="80">
			</el-table-column>

			<el-table-column prop="createTime" header-align="center" align="center" label="申请时间" width="150">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.createTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.createTime).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="cashProcessNodeStatus" header-align="center" align="center" label="审核状态"  width="80">
				<template slot-scope="scope">
					<span v-if="scope.row.cashProcessNodeStatus == 0">待处理</span>
					<span v-else-if="scope.row.cashProcessNodeStatus == 2">通过</span>
					<span v-else-if="scope.row.cashProcessNodeStatus == 3">驳回</span>
				</template>
			</el-table-column>
			<el-table-column prop="updateTime" header-align="center" align="center" label="审核时间"  width="150">
				<template slot-scope="scope">
					<span v-if="scope.row.updateTime == 0"></span>
					<span v-else>{{new Date(scope.row.updateTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.updateTime).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="paymentCode" header-align="center" align="center" label="交易流水号"  width="180">
			</el-table-column>
			<el-table-column prop="note" header-align="center" align="center" label="备注">
			</el-table-column>
			<el-table-column prop="" header-align="center" align="center" label="操作">
				<template slot-scope="scope">
					<el-button v-if="scope.row.cashProcessNodeStatus == 0" type="primary" size="mini" @click="passHandle(scope.row.uuid)">审核通过</el-button>
					<el-button v-if="scope.row.cashProcessNodeStatus == 0" type="danger" size="mini" @click="refuseshow(scope.row.uuid)" style="margin: 0;margin-top: 10px;">审核驳回</el-button>
				</template>
			</el-table-column>
		</el-table>
		<el-dialog title="审核驳回" :visible.sync="refiseVisible" :append-to-body="false">
			<el-input type="textarea" :rows="4" v-model="note" placeholder="请输入驳回原因"></el-input>
			<span slot="footer" class="dialog-footer">
	        	<el-button @click="refiseVisible = false">取消</el-button>
	        	<el-button type="primary" @click="refuseHandle()">确定</el-button>
	      	</span>
		</el-dialog>
		<el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex" :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage" layout="total, sizes, prev, pager, next, jumper"></el-pagination>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				dataList: [],
				condition: {
					recordType: '5',
					userId: '',
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
					userId: '',
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
					url: this.$http.adornUrl("/sale/saleMemberCashApply/list"),
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
			passHandle(uuid) {
				var formData = new FormData();
				formData.append("uuid", uuid);
				this.$http.post(this.$http.adornUrl("/sale/saleBuyer/cashOutExamine"), formData)
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
			},
			refuseshow(uuid){
				this.refiseVisible = true
				this.curid = uuid
				this.note = ''
			},
			refuseHandle(){
				var formData = new FormData();
				formData.append("uuid", this.curid);
				formData.append("note", this.note);
				this.$http.post(this.$http.adornUrl("/sale/saleBuyer/cashOutRefuse"), formData)
					.then(({
						data
					}) => {
						if(data && data.code === 0) {
							this.$message({
								message: "操作成功",
								type: "success",
								duration: 1500,
								onClose: () => {
									this.refiseVisible = false
									this.getDataList();
								}
							});
						} else {
							this.$message.error(data.msg);
						}
					});
			}
		}
	};
</script>

<style scoped>

</style>
