<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>订单评价管理</h4>
		</div>
		<div class="tool-header">
			<el-form :inline="true" :model="condition" @keyup.enter.native="getDataList()">
				<el-form-item>
					<el-input v-model="condition.sellerName" placeholder="商家名称" size="mini" class="w120"></el-input>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.userNick" placeholder="用户昵称" size="mini" class="w120"></el-input>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.feedbackStar" size="mini" clearable placeholder="反馈星级">
						<el-option label="非常差" value="1"></el-option>
						<el-option label="差" value="2"></el-option>
						<el-option label="一般" value="3"></el-option>
						<el-option label="好" value="4"></el-option>
						<el-option label="非常好" value="5"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.feedbackCheckStatus" size="mini" clearable placeholder="审核状态">
						<el-option label="未审核" value="0"></el-option>
						<el-option label="已通过" value="1"></el-option>
						<el-option label="已驳回" value="2"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.hasWord" size="mini" clearable placeholder="有无文字">
						<el-option label="有" value="true"></el-option>
						<el-option label="无" value="false"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.hasPicture" size="mini" clearable placeholder="有无图片">
						<el-option label="有" value="true"></el-option>
						<el-option label="无" value="false"></el-option>
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
			<el-table-column prop="sellerName" header-align="center" align="center" label="店铺名称">
			</el-table-column>
			<el-table-column prop="userId" header-align="center" align="center" label="用户编码">
			</el-table-column>
			<el-table-column prop="userNick" header-align="center" align="center" label="用户昵称">
			</el-table-column>
			<el-table-column prop="sellerCode" header-align="center" align="center" label="商家编码">
			</el-table-column>
			<el-table-column prop="feedbackStar" header-align="center" align="center" label="反馈星级">
				<template slot-scope="scope">
					<el-tag type="danger" v-if="scope.row.feedbackStar == 1">非常差</el-tag>
					<el-tag type="danger" v-if="scope.row.feedbackStar == 2">差</el-tag>
					<el-tag v-if="scope.row.feedbackStar == 3">一般</el-tag>
					<el-tag type="success" v-if="scope.row.feedbackStar == 4">好</el-tag>
					<el-tag type="success" v-if="scope.row.feedbackStar == 5">非常好</el-tag>
				</template>
			</el-table-column>
			<el-table-column prop="feedbackLable" header-align="center" align="center" label="反馈标签">
			</el-table-column>
			<el-table-column prop="feedbackNote" width="100" header-align="center" align="center" label="反馈内容">
				<template slot-scope="scope">
					<el-tooltip class="item" effect="dark" :content="scope.row.feedbackNote" placement="left">
						<span class="feednote">{{scope.row.feedbackNote}}</span>
					</el-tooltip>
				</template>
			</el-table-column>
			<el-table-column prop="feedbackUrl" header-align="center" align="center" label="反馈图片">
				<template slot-scope="scope">
					<img :src="scope.row.feedbackUrl[0]" @click="showImage(scope.row.feedbackUrl)" alt="" style="width: 100%;display: block;margin: 0 auto;">
				</template>
			</el-table-column>
			<el-table-column prop="createTime" header-align="center" align="center" label="评价时间">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.createTime).toLocaleDateString().replace(/\//g, "-")}} {{new Date(scope.row.createTime).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="feedbackCheckStatus" header-align="center" align="center" label="审核状态">
				<template slot-scope="scope">
					<el-tag v-if="scope.row.feedbackCheckStatus == 0">未审核</el-tag>
					<el-tag type="success" v-if="scope.row.feedbackCheckStatus == 1">已审核</el-tag>
					<el-tag type="danger" v-if="scope.row.feedbackCheckStatus == 2">已驳回</el-tag>
				</template>
			</el-table-column>
			<el-table-column prop="updateTime" header-align="center" align="center" label="审核时间">
				<template slot-scope="scope">
					<span v-if="scope.row.updateTime == 0"></span>
					<span v-else>{{new Date(scope.row.updateTime).toLocaleDateString().replace(/\//g, "-")}} {{new Date(scope.row.updateTime).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="note" header-align="center" align="center" label="备注">
			</el-table-column>
			<el-table-column prop="" header-align="center" align="center" label="操作">
				<template slot-scope="scope">
					<el-button type="primary" size="mini" @click="passHandle(scope.row.id)">审核通过</el-button>
					<el-button type="danger" size="mini" @click="refuseHandle(scope.row.id)" style="margin: 0;margin-top: 10px;">审核驳回</el-button>
				</template>
			</el-table-column>
		</el-table>
		<div class="btn-warper">
			<el-button type="primary" size="mini" @click="allPassHandle">审核通过</el-button>
			<el-button type="danger" size="mini" @click="allrefuseHandle">审核通过</el-button>
		</div>
		<el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex" :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage" layout="total, sizes, prev, pager, next, jumper"></el-pagination>
		<el-dialog title="评价图片" :visible.sync="imageVisible" :append-to-body="false">
			<el-carousel indicator-position="outside" :autoplay="false">
				<el-carousel-item v-for="item in curImagelist" :key="item">
					<img :src="item" alt="" style="height: 240px;display: block;margin: 30px auto;">
				</el-carousel-item>
			</el-carousel>
		</el-dialog>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				dataList: [],
				condition: {
					sellerName: '',
					userNick: '',
					feedbackStar: '',
					feedbackCheckStatus: '',
					hasWord: '',
					hasPicture: '',
					page: 1,
					size: 10
				},
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false,
				imageVisible: false,
				curImagelist: []
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
					sellerName: '',
					userNick: '',
					feedbackStar: '',
					feedbackCheckStatus: '',
					hasWord: '',
					hasPicture: '',
					page: 1,
					size: 10
				}
			},
			
			showImage(data){
				this.curImagelist = data
				console.log(this.curImagelist)
				this.imageVisible = true
			},
			
			// 获取数据列表
			getDataList() {
				this.dataListLoading = true;
				this.condition.page = this.pageIndex
				this.condition.size = this.pageSize
				this.$http({
					url: this.$http.adornUrl("/sale/saleOrderAssess/list"),
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
						for(var i = 0; i < this.dataList.length; i++) {
							if(this.dataList[i].feedbackUrl) {
								this.dataList[i].feedbackUrl = this.dataList[i].feedbackUrl.split(",")
							}
						}
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
			passHandle(id) {
				var formData = new FormData();
				formData.append("ids", [id]);
				formData.append("status", 1);
				this.$http.post(this.$http.adornUrl("/sale/saleOrderAssess/updateAssessStatus"), formData)
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
			allPassHandle(){
				if(this.dataListSelections.length === 0) {
					this.$message({
						type: 'error',
						message: '请至少选择一条记录查看'
					})
					return false
				}
				
				var len = this.dataListSelections.length
				var ids = []
				for(var i = 0 ;i < len; i++){
					ids.push(this.dataListSelections[i].id)
				}
				var formData = new FormData();
				formData.append("ids", ids);
				formData.append("status", 1);
				this.$http.post(this.$http.adornUrl("/sale/saleOrderAssess/updateAssessStatus"), formData)
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
			refuseHandle(id) {
				var formData = new FormData();
				formData.append("ids", [id]);
				formData.append("status", 2);
				this.$http.post(this.$http.adornUrl("/sale/saleOrderAssess/updateAssessStatus"), formData)
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
			allrefuseHandle() {
				if(this.dataListSelections.length === 0) {
					this.$message({
						type: 'error',
						message: '请至少选择一条记录查看'
					})
					return false
				}
				
				var len = this.dataListSelections.length
				var ids = []
				for(var i = 0 ;i < len; i++){
					ids.push(this.dataListSelections[i].id)
				}
				var formData = new FormData();
				formData.append("ids", ids);
				formData.append("status", 2);
				this.$http.post(this.$http.adornUrl("/sale/saleOrderAssess/updateAssessStatus"), formData)
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
			}
		}
	};
</script>

<style>
	.feednote {
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
		position: relative;
	}
	
	.el-tooltip__popper.is-dark {
		max-width: 400px !important;
		line-height: 20px;
	}
</style>