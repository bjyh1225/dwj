<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>消息推送</h4>
			<div class="btns">
				<el-button type="primary" size="mini" @click="addshow()">新增</el-button>
			</div>
		</div>
		<div class="tool-header">
			<el-form :inline="true" :model="condition" @keyup.enter.native="getDataList()">
				<el-form-item>
					<el-select v-model="condition.pushObjectType" size="mini" clearable placeholder="推送目标">
						<el-option label="会员" value="0"></el-option>
						<el-option label="商家" value="1"></el-option>
						<el-option label="会员和商家" value="2"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.newsTitle" placeholder="标题" size="mini" class="w120"></el-input>
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
			<el-table-column prop="pushObjectType" header-align="center" align="center" label="推送目标类别">
				<template slot-scope="scope">
					<span v-if="scope.row.pushObjectType == 0">会员</span>
					<span v-else-if="scope.row.pushObjectType == 1">商家</span>
					<span v-else>会员和商家</span>
				</template>
			</el-table-column>
			<el-table-column prop="newsCode" header-align="center" align="center" label="信息编码">
			</el-table-column>
			<el-table-column prop="newsTitle" header-align="center" align="center" label="信息标题">
			</el-table-column>
			<el-table-column prop="newsContent" header-align="center" align="center" label="信息内容">
				<template slot-scope="scope">
					<el-tooltip class="item" effect="dark" :content="scope.row.newsContent"  placement="left">
						<span class="feednote">{{scope.row.newsContent}}</span>
					</el-tooltip>
				</template>
			</el-table-column>
			<el-table-column prop="showUrl" header-align="center" align="center" label="消息图片">
				<template slot-scope="scope">
					<img :src="scope.row.showUrl" alt="" style="width: 100%;display: block;margin: 0 auto;">
				</template>
			</el-table-column>
			<el-table-column prop="pushBatch" header-align="center" align="center" label="推送批次">
			</el-table-column>
			<!--<el-table-column prop="status" header-align="center" align="center" label="状态">
				<template slot-scope="scope">
					<span v-if="scope.row.status == 0">启用</span>
					<span v-else-if="scope.row.status == 1">禁用</span>
				</template>
			</el-table-column>-->
			<el-table-column prop="updateUserType" header-align="center" align="center" label="更新人类型">
				<template slot-scope="scope">
					<span v-if="scope.row.updateUserType == 0">平台</span>
					<span v-else-if="scope.row.updateUserType == 1">商家</span>
					<span v-else-if="scope.row.updateUserType == 2">消费者</span>
				</template>
			</el-table-column>
			<el-table-column prop="updateTime" header-align="center" align="center" label="更新时间">
				<template slot-scope="scope">
					<span v-if="scope.row.updateTime == 0"></span>
					<span v-else>{{new Date(scope.row.updateTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(scope.row.updateTime).toTimeString().substr(0, 5)}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="" header-align="center" align="center" label="操作">
				<template slot-scope="scope">
					<el-button type="primary" size="mini" @click="pushhandel(scope.row.newsCode)">推送</el-button>
				</template>
			</el-table-column>
		</el-table>
		<div class="btn-warper">
			<el-button type="primary" size="mini" @click="editshow">修改</el-button>
			<el-button type="danger" size="mini" @click="deleteHandle">删除</el-button>
		</div>
		<el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex" :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage" layout="total, sizes, prev, pager, next, jumper"></el-pagination>
		<el-dialog title="新增" :close-on-click-modal="false" :visible.sync="addvisible" width="760px">
			<el-form :model="dataForm" :rules="dataRule" ref="dataForm" label-width="160px" class="w-80">
				<el-form-item label="推送目标" prop="pushObjectType">
					<el-select v-model="dataForm.pushObjectType" size="mini" clearable placeholder="推送目标">
						<el-option label="会员" value="0"></el-option>
						<el-option label="商家" value="1"></el-option>
						<el-option label="会员和商家" value="2"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item label="标题" prop="newsTitle">
					<el-input v-model="dataForm.newsTitle" placeholder="标题"></el-input>
				</el-form-item>
				<el-form-item label="站内信息图片" prop="noticePicture">
					<el-upload :action="uploadUrl" list-type="picture-card" accept="image/*" :limit="1" :file-list="PictureImgs" :on-preview="logoPreview" :on-success="handleAvatarSuccess" :before-upload="logorUpload">
						<i class="el-icon-plus"></i>
					</el-upload>
				</el-form-item>
				<el-form-item label="内容" prop="newsContent">
					<el-input type="textarea" :rows="6" v-model="dataForm.newsContent" placeholder="内容"></el-input>
				</el-form-item>
				<!--<el-form-item label="站内信息显示方式" prop="showType">
					<el-radio-group v-model="dataForm.showType">
						<el-radio :label="0">文本</el-radio>
						<el-radio :label="1">页面</el-radio>
					</el-radio-group>
				</el-form-item>-->
				<!--<el-form-item label="状态" prop="status">
					<el-radio-group v-model="dataForm.status">
						<el-radio :label="0">启用</el-radio>
						<el-radio :label="1">禁用</el-radio>
					</el-radio-group>
				</el-form-item>-->
				<el-form-item label="备注" prop="note">
					<el-input v-model="dataForm.note" placeholder="备注"></el-input>
				</el-form-item>
			</el-form>
			<span slot="footer" class="dialog-footer">
        		<el-button @click="addvisible = false">取消</el-button>
        		<el-button type="primary" @click="adddataFormSubmit()">确定</el-button>
      		</span>
		</el-dialog>

		<el-dialog title="修改" :close-on-click-modal="false" :visible.sync="editvisible" width="760px">
			<el-form :model="dataForm" :rules="dataRule" ref="dataForm" label-width="160px" class="w-80">
				<el-form-item label="推送目标" prop="pushObjectType">
					<el-select v-model="dataForm.pushObjectType" size="mini" clearable placeholder="推送目标">
						<el-option label="会员" value="0"></el-option>
						<el-option label="商家" value="1"></el-option>
						<el-option label="会员和商家" value="2"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item label="信息编码" prop="newsCode">
					<el-input v-model="dataForm.newsCode" :disabled="true" placeholder="信息编码"></el-input>
				</el-form-item>
				<el-form-item label="标题" prop="newsTitle">
					<el-input v-model="dataForm.newsTitle" placeholder="标题"></el-input>
				</el-form-item>
				<el-form-item label="站内信息图片" prop="noticePicture">
					<el-upload :action="uploadUrl" list-type="picture-card" accept="image/*" :limit="1" :file-list="PictureImgs" :on-preview="logoPreview" :on-success="handleAvatarSuccess" :before-upload="logorUpload">
						<i class="el-icon-plus"></i>
					</el-upload>
				</el-form-item>
				<el-form-item label="内容" prop="newsContent">
					<el-input type="textarea" :rows="6" v-model="dataForm.newsContent" placeholder="内容"></el-input>
				</el-form-item>
				<!--<el-form-item label="站内信息显示方式" prop="showType">
					<el-radio-group v-model="dataForm.showType">
						<el-radio :label="0">文本</el-radio>
						<el-radio :label="1">页面</el-radio>
					</el-radio-group>
				</el-form-item>-->
				<!--<el-form-item label="状态" prop="status">
					<el-radio-group v-model="dataForm.status">
						<el-radio :label="0">启用</el-radio>
						<el-radio :label="1">禁用</el-radio>
					</el-radio-group>
				</el-form-item>-->
				<el-form-item label="备注" prop="note">
					<el-input v-model="dataForm.note" placeholder="备注"></el-input>
				</el-form-item>
			</el-form>
			<span slot="footer" class="dialog-footer">
        		<el-button @click="editvisible = false">取消</el-button>
        		<el-button type="primary" @click="dataFormSubmit()">确定</el-button>
      		</span>
		</el-dialog>
		<el-dialog :visible.sync="visible" :append-to-body="false">
			<img width="100%" :src="Picture" alt="">
		</el-dialog>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				dataList: [],
				condition: {
					pushObjectType: '',
					newsTitle: ''
				},
				dataForm: {
					pushObjectType: '',
					newsTitle: '',
					newsContent: '',
					showType: 0,
					showUrl: '',
//					status: 0,
					note: ''
				},
				dataRule: {
					pushObjectType: [{
						required: true,
						message: '推送目标不能为空',
						trigger: 'change'
					}],
					newsTitle: [{
						required: true,
						message: '标题不能为空',
						trigger: 'blur'
					}],
					newsContent: [{
						required: true,
						message: '内容不能为空',
						trigger: 'blur'
					}],
				},
				types: [],
				dataListSelections: [],
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false,
				addvisible: false,
				editvisible: false,
				visible: false,
				Picture: '',
				PictureImgs: [],
				uploadUrl: 'http://192.168.1.121:13001/dwj-web-oss/oss/uploadFileToDir?dirName=seller'
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
					newsTitle: ''
				}
			},
			// 获取数据列表
			getDataList() {
				this.dataListLoading = true;
				this.condition.page = this.pageIndex
				this.condition.size = this.pageSize
				this.$http({
					url: this.$http.adornUrl("/sale/salePushNews/list"),
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
			addshow() {
				this.dataForm = {
					pushObjectType: '',
					newsTitle: '',
					newsContent: '',
					showType: 0,
					showUrl: '',
//					status: 0,
					note: ''
				}
				this.PictureImgs = []
				this.addvisible = true
			},
			editshow() {
				if(this.dataListSelections.length === 0) {
					this.$message({
						type: 'error',
						message: '请至少选择一条记录查看'
					})
					return false
				}
				if(this.dataListSelections.length > 1) {
					this.$message({
						type: 'error',
						message: '只能选择一条记录查看'
					})
					return false
				}
				this.editvisible = true;
				//获取内容
				this.dataForm = {
					pushObjectType: this.dataListSelections[0].pushObjectType.toString(),
					newsTitle: this.dataListSelections[0].newsTitle,
					newsCode: this.dataListSelections[0].newsCode,
					newsContent: this.dataListSelections[0].newsContent,
					showType: this.dataListSelections[0].showType,
					showUrl: this.dataListSelections[0].showUrl,
//					status: this.dataListSelections[0].status,
					note: this.dataListSelections[0].note   
				}
				
				if(this.dataListSelections[0].showUrl) {
						let img = {
							name: '消息图片',
							url: this.dataListSelections[0].showUrl
						}
						this.PictureImgs = []
						this.PictureImgs.push(img)
				}else {
					this.PictureImgs = []
				}
				
			},
			logoPreview(file) { //预览图片时调用
				this.Picture = file.url;
				this.visible = true;
			},
			logorUpload(file) {
				var formData = new FormData();
				formData.append("file", file);
				this.$http.post("http://192.168.1.121:13001/dwj-web-oss/oss/uploadFileToDir?dirName=seller", formData)
					.then(({
						data
					}) => {
						this.dataForm.showUrl = data.data
					});
			},
			handleAvatarSuccess(file) { //图片上传成功
				console.log("上传成功")
			},
			//表单提交
			adddataFormSubmit() {
				this.$refs["dataForm"].validate(valid => {
					if(valid) {
						this.$http.post(this.$http.adornUrl("/sale/salePushNews/save"), this.dataForm)
							.then(({
								data
							}) => {
							if(data && data.code === 0) {
								this.$message({
									message: "操作成功",
									type: "success",
									duration: 1500,
									onClose: () => {
										this.addvisible = false;
										this.editvisible = false;
									}
								});
								this.getDataList();
							} else {
								this.$message.error(data.msg);
							}
						});
					}
				});
			},
			//表单提交
			dataFormSubmit() {
				this.$refs["dataForm"].validate(valid => {
					if(valid) {
						this.$http.post(this.$http.adornUrl("/sale/salePushNews/update"), this.dataForm)
							.then(({
								data
							}) => {
							if(data && data.code === 0) {
								this.$message({
									message: "操作成功",
									type: "success",
									duration: 1500,
									onClose: () => {
										this.addvisible = false;
										this.editvisible = false;
									}
								});
								this.getDataList();
							} else {
								this.$message.error(data.msg);
							}
						});
					}
				});
			},
			pushhandel(code) {
				var formData = new FormData();
				formData.append("pushNewsCode", code);
				this.$http.post(this.$http.adornUrl("/sale/salePushNews/doPushNews"), formData)
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
			// 删除
			deleteHandle() {
				if(this.dataListSelections.length === 0) {
					this.$message({
						type: 'error',
						message: '请至少选择一条记录查看'
					})
					return false
				}
				this.$confirm(`确定对当前行进行操作?`, "提示", {
						confirmButtonText: "确定",
						cancelButtonText: "取消",
						type: "warning"
					})
					.then(() => {
						let ids = []
						this.dataListSelections.forEach(function(e) {
							ids.push(e.uuid)
						});
						var formData = new FormData();
						formData.append("ids", ids);
						this.$http.post(this.$http.adornUrl("/sale/salePushNews/delete"), formData)
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