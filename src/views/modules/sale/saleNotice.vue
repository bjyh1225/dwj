<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>公告管理</h4>
			<div class="btns">
				<el-button type="primary" size="mini" @click="addHandle()">新增</el-button>
			</div>
		</div>
		<div class="tool-header">
			<el-form :inline="true" :model="condition" @keyup.enter.native="getDataList()">
				<el-form-item>
					<el-select size="mini" v-model="condition.industryTypeCode" placeholder="行业类别" clearable class="w-100">
						<el-option v-for="item in types" :key="item.value" :label="item.label" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.createUserType" size="mini" clearable placeholder="发布主体">
						<el-option label="平台" value="0"></el-option>
						<el-option label="商家" value="1"></el-option>
						<el-option label="消费者" value="2"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.noticeType" size="mini" clearable placeholder="公告类别">
						<el-option label="文字" value="0"></el-option>
						<el-option label="图片" value="1"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.noticeStatus" size="mini" clearable placeholder="发布状态">
						<el-option label="已发布" value="0"></el-option>
						<el-option label="未发布" value="1"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.noticeLable" placeholder="发布标题" size="mini" class="w120"></el-input>
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
			<el-table-column prop="industryTypeName" header-align="center" align="center" label="行业类别">
			</el-table-column>
			<el-table-column prop="noticeOrder" header-align="center" align="center" label="公告序号">
			</el-table-column>
			<el-table-column prop="noticeCode" header-align="center" align="center" label="公告编码">
			</el-table-column>
			<el-table-column prop="noticeType" header-align="center" align="center" label="公告类别">
				<template slot-scope="scope">
					<span v-if="scope.row.noticeType == 0">文字</span>
					<span v-else-if="scope.row.noticeType == 1">图片</span>
				</template>
			</el-table-column>
			<el-table-column prop="noticeStatus" header-align="center" align="center" label="公告状态">
				<template slot-scope="scope">
					<span v-if="scope.row.noticeStatus == 0">已发布</span>
					<span v-else-if="scope.row.noticeStatus == 1">未发布</span>
				</template>
			</el-table-column>
			<el-table-column prop="sellerCode" header-align="center" align="center" label="商家编码">
			</el-table-column>
			<el-table-column prop="fullName" header-align="center" align="center" label="商家名称">
			</el-table-column>
			<el-table-column prop="noticePicture" header-align="center" align="center" label="公告图片" width="120">
				<template slot-scope="scope">
					<img :src="scope.row.noticePicture" alt="" style="width: 100px;display: block;margin: 0 auto;">
				</template>
			</el-table-column>
			<el-table-column prop="noticePictureUrl" header-align="center" align="center" label="公告图片链接">
			</el-table-column>
			<el-table-column prop="noticeLable" header-align="center" align="center" label="公告标题">
			</el-table-column>
			<el-table-column prop="noticeText" header-align="center" align="center" label="公告详情">
			</el-table-column>
			<el-table-column prop="status" header-align="center" align="center" label="状态">
				<template slot-scope="scope">
					<span v-if="scope.row.status == 0">启用</span>
					<span v-else-if="scope.row.status == 1">禁用</span>
				</template>
			</el-table-column>
			<el-table-column prop="note" header-align="center" align="center" label="备注">
			</el-table-column>
			<el-table-column prop="createUserType" header-align="center" align="center" label="创建人">
				<template slot-scope="scope">
					<span v-if="scope.row.createUserType == 0">平台</span>
					<span v-else-if="scope.row.createUserType == 1">商家</span>
					<span v-else-if="scope.row.createUserType == 2">消费者</span>
				</template>
			</el-table-column>
			<el-table-column prop="createTime" header-align="center" align="center" label="创建时间" width="180">
				<template slot-scope="scope">
					<span v-if="scope.row.createTime == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.createTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.createTime).toTimeString().substr(0, 8)}}</span>
					</span>
				</template>
			</el-table-column>
			<el-table-column prop="updateUserType" header-align="center" align="center" label="更新人">
				<template slot-scope="scope">
					<span v-if="scope.row.updateUserType == 0">平台</span>
					<span v-else-if="scope.row.updateUserType == 1">商家</span>
					<span v-else-if="scope.row.updateUserType == 2">消费者</span>
				</template>
			</el-table-column>
			<el-table-column prop="updateTime" header-align="center" align="center" label="更新时间" width="180">
				<template slot-scope="scope">
					<span v-if="scope.row.updateTime == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.updateTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.updateTime).toTimeString().substr(0, 8)}}</span>
					</span>
				</template>
			</el-table-column>
		</el-table>
		<div class="btn-warper">
			<el-button type="primary" size="mini" @click="editHandle">修改</el-button>
			<el-button type="danger" size="mini" @click="deleteHandle">删除</el-button>
		</div>
		<el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex" :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage" layout="total, sizes, prev, pager, next, jumper"></el-pagination>

		<el-dialog title="新增" :close-on-click-modal="false" :visible.sync="addvisible" width="760px">
			<el-form :model="dataForm" :rules="dataRule" ref="dataForm" label-width="160px" class="w-80">
				<el-form-item label="行业类别" prop="industryTypeCode">
					<el-select size="mini" v-model="dataForm.industryTypeCode" placeholder="行业类别">
						<el-option v-for="item in types" :key="item.value" :label="item.label" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item label="序号" prop="noticeOrder">
					<el-input v-model="dataForm.noticeOrder" placeholder="序号"></el-input>
				</el-form-item>
				<el-form-item label="公告类别" size="mini" prop="noticeType">
					<el-radio-group v-model="dataForm.noticeType">
						<el-radio :label="0">文字</el-radio>
						<el-radio :label="1">图片</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="公告状态" size="mini" prop="noticeStatus">
					<el-radio-group v-model="dataForm.noticeStatus">
						<el-radio :label="0">已发布</el-radio>
						<el-radio :label="1">未发布</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="商家名称" prop="sellerCode">
					<el-select v-model="dataForm.sellerCode" clearable placeholder="商家名称" clearable class="w-100">
						<el-option v-for="item in allsellers" :key="item.index" :label="item.name" :value="item.value"></el-option>
					</el-select>
				</el-form-item>

				<el-form-item label="公告图片" prop="noticePicture">
					<el-upload :action="uploadUrl" list-type="picture-card" accept="image/*" :limit="1" :file-list="noticePictureImgs" :on-preview="logoPreview" :on-success="handleAvatarSuccess" :before-upload="logorUpload">
						<i class="el-icon-plus"></i>
					</el-upload>
				</el-form-item>
				<el-form-item label="公告图片链接URL" prop="noticePictureUrl">
					<el-input v-model="dataForm.noticePictureUrl" placeholder="公告图片链接URL"></el-input>
				</el-form-item>
				<el-form-item label="公告标题" prop="noticeLable">
					<el-input v-model="dataForm.noticeLable" placeholder="公告标题"></el-input>
				</el-form-item>
				<el-form-item label="公告详情" prop="noticeText">
					<el-input v-model="dataForm.noticeText" placeholder="公告详情"></el-input>
				</el-form-item>
				<el-form-item label="状态" size="mini" prop="status">
					<el-radio-group v-model="dataForm.status">
						<el-radio :label="0">启用</el-radio>
						<el-radio :label="1">禁用</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="备注" prop="note">
					<el-input v-model="dataForm.note" placeholder="备注"></el-input>
				</el-form-item>
			</el-form>
			<span slot="footer" class="dialog-footer">
        		<el-button @click="addvisible = false">取消</el-button>
        		<el-button type="primary" @click="addDataFormSubmit()">确定</el-button>
      		</span>
		</el-dialog>

		<el-dialog title="修改" :close-on-click-modal="false" :visible.sync="editvisible" width="760px">
			<el-form :model="dataForm" :rules="dataRule" ref="dataForm" label-width="160px" class="w-80">
				<el-form-item label="行业类别" prop="industryTypeCode">
					<el-select size="mini" v-model="dataForm.industryTypeCode" placeholder="行业类别">
						<el-option v-for="item in types" :key="item.value" :label="item.label" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item label="序号" prop="noticeOrder">
					<el-input v-model="dataForm.noticeOrder" placeholder="序号"></el-input>
				</el-form-item>
				<el-form-item label="公告编码" prop="noticeCode">
					<el-input v-model="dataForm.noticeCode" :disabled="true" placeholder="编码"></el-input>
				</el-form-item>
				<el-form-item label="公告类别" size="mini" prop="noticeType">
					<el-radio-group v-model="dataForm.noticeType">
						<el-radio :label="0">文字</el-radio>
						<el-radio :label="1">图片</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="公告状态" size="mini" prop="noticeStatus">
					<el-radio-group v-model="dataForm.noticeStatus">
						<el-radio :label="0">已发布</el-radio>
						<el-radio :label="1">未发布</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="商家名称" prop="sellerCode">
					<el-select v-model="dataForm.sellerCode" clearable placeholder="商家名称" clearable class="w-100">
						<el-option v-for="item in allsellers" :key="item.index" :label="item.name" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item label="公告图片" prop="noticePicture">
					<el-upload :action="uploadUrl" list-type="picture-card" accept="image/*" :limit="1" :file-list="noticePictureImgs" :on-preview="logoPreview" :on-success="handleAvatarSuccess" :before-upload="logorUpload">
						<i class="el-icon-plus"></i>
					</el-upload>
				</el-form-item>
				<el-form-item label="公告图片链接URL" prop="noticePictureUrl">
					<el-input v-model="dataForm.noticePictureUrl" placeholder="公告图片链接URL"></el-input>
				</el-form-item>
				<el-form-item label="公告标题" prop="noticeLable">
					<el-input v-model="dataForm.noticeLable" placeholder="公告标题"></el-input>
				</el-form-item>
				<el-form-item label="公告详情" prop="noticeText">
					<el-input v-model="dataForm.noticeText" placeholder="公告详情"></el-input>
				</el-form-item>
				<el-form-item label="状态" size="mini" prop="status">
					<el-radio-group v-model="dataForm.status">
						<el-radio :label="0">启用</el-radio>
						<el-radio :label="1">禁用</el-radio>
					</el-radio-group>
				</el-form-item>
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
			<img width="100%" :src="noticePicture" alt="">
		</el-dialog>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				dataList: [],
				condition: {
					industryTypeCode: '',
					createUserType: '',
					noticeType: '',
					noticeStatus: '',
					noticeLable: '',
					page: 1,
					size: 10
				},
				dataForm: {
					uuid: '',
					industryTypeCode: '',
					noticeCode: '',
					noticeOrder: '',
					noticeType: 0,
					noticeStatus: '',
					sellerCode: '',
					noticePicture: '',
					noticePictureUrl: '',
					noticeLable: '',
					noticeText: 0,
					status: '',
					note: ''
				},
				dataRule: {
					industryTypeCode: [{
						required: true,
						message: '行业类别不能为空',
						trigger: 'change'
					}],
					sellerCode: [{
						required: true,
						message: '商家名称不能为空',
						trigger: 'change'
					}],
					noticeLable: [{
						required: true,
						message: '公告标题不能为空',
						trigger: 'blur'
					}]
				},
				types: [],
				allsellers: [],
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false,
				dataListSelections: [],
				editvisible: false,
				addvisible: false,
				linVisible: false,
				noticePicture: '',
				visible: false,
				noticePictureImgs: [],
				uploadUrl: 'http://192.168.1.121:13001/dwj-web-oss/oss/uploadFileToDir?dirName=seller'
			};
		},
		activated() {
			this.getDataList()
			this.getTypes()
			this.allsellers = []
			this.showSeller()
		},
		methods: {
			searchHndel() {
				this.pageIndex = 1
				this.pageSize = 10
				this.getDataList()
			},
			searchReset() {
				this.condition = {
					industryTypeCode: '',
					createUserType: '',
					noticeType: '',
					noticeStatus: '',
					noticeLable: '',
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
					this.allsellers.unshift(
						{
							name: "平台",
							value: "000"
						}
					)
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
					url: this.$http.adornUrl("/sale/saleNotice/list"),
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

			//新增
			addHandle() {
				this.dataForm = {
					uuid: '',
					industryTypeCode: '',
					code: '',
					name: '',
					noticeType: 0,
					noticeStatus: 0,
					sellerCode: '',
					noticePicture: '',
					noticePictureUrl: '',
					noticeLable: '',
					noticeText: '',
					status: 0,
					note: ''
				}
				this.noticePictureImgs = []
				this.addvisible = true
			},
			logoPreview(file) { //预览图片时调用
				this.noticePicture = file.url;
				this.visible = true;
			},
			logorUpload(file) {
				var formData = new FormData();
				formData.append("file", file);
				this.$http.post("http://192.168.1.121:13001/dwj-web-oss/oss/uploadFileToDir?dirName=seller", formData)
					.then(({
						data
					}) => {
						this.dataForm.noticePicture = data.data
					});
			},
			handleAvatarSuccess(file) { //图片上传成功
				console.log("上传成功")
			},

			//新增表单提交
			addDataFormSubmit() {
				if(this.dataForm.noticePicture == ''){
					this.$message({
						type: 'error',
						message: '公告图片不能为空'
					})
					return false
				}
				this.$refs["dataForm"].validate(valid => {
					if(valid) {
						this.$http.post(this.$http.adornUrl("/sale/saleNotice/save"), this.dataForm)
							.then(({
								data
							}) => {
								if(data && data.code === 0) {
									this.$message({
										message: "操作成功",
										type: "success",
										duration: 1500,
										onClose: () => {
											this.addvisible = false
											this.getDataList();
										}
									});
								} else {
									this.$message.error(data.msg);
								}
							});
					}
				})

			},

			//修改
			editHandle() {
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
				this.editvisible = true
				this.dataForm = this.dataListSelections[0]
				this.noticePictureImgs = []
				if(this.dataListSelections[0].noticePicture) {
					let img = {
						name: '公告图片',
						url: this.dataListSelections[0].noticePicture
					}
					this.noticePictureImgs.push(img)
				}

			},

			//修改表单提交
			dataFormSubmit() {
				if(this.dataForm.noticePicture == ''){
					this.$message({
						type: 'error',
						message: '公告图片不能为空'
					})
					return false
				}
				
				this.$refs["dataForm"].validate(valid => {
					if(valid) {
						this.$http.post(this.$http.adornUrl("/sale/saleNotice/update"), this.dataForm)
							.then(({
								data
							}) => {
								if(data && data.code === 0) {
									this.$message({
										message: "操作成功",
										type: "success",
										duration: 1500,
										onClose: () => {
											this.editvisible = false
											this.getDataList();
										}
									});
								} else {
									this.$message.error(data.msg);
								}
							});
					}
				})

			},

			// 删除
			deleteHandle(id) {
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
						this.$http.post(this.$http.adornUrl("/sale/saleNotice/delete"), formData)
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
		}
	};
</script>

<style scoped>

</style>