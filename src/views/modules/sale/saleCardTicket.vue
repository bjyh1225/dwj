<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>卡券管理</h4>
			<div class="btns">
				<el-button type="primary" size="mini" @click="addHandle()">新增</el-button>
			</div>
		</div>
		<div class="tool-header">
			<el-form :inline="true" :model="condition" @keyup.enter.native="getDataList()">
				<el-form-item>
					<el-select v-model="condition.cardType" size="mini" clearable placeholder="主体类别">
						<el-option label="会员" value="0"></el-option>
						<el-option label="商家" value="1"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.termType" size="mini" clearable placeholder="期限类别">
						<el-option label="年卡" value="0"></el-option>
						<el-option label="季卡" value="1"></el-option>
						<el-option label="月卡" value="2"></el-option>
						<el-option label="不定期限" value="3"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.status" size="mini" clearable placeholder="状态">
						<el-option label="启用" value="0"></el-option>
						<el-option label="禁用" value="1"></el-option>
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
			<el-table-column prop="cardType" header-align="center" align="center" label="主体类别">
				<template slot-scope="scope">
					<span v-if="scope.row.cardType == 0">会员</span>
					<span v-else-if="scope.row.cardType == 1">商家</span>
				</template>
			</el-table-column>
			<el-table-column prop="termType" header-align="center" align="center" label="期限类别">
				<template slot-scope="scope">
					<span v-if="scope.row.termType == 0">年卡</span>
					<span v-else-if="scope.row.termType == 1">季卡</span>
					<span v-else-if="scope.row.termType == 2">月卡</span>
					<span v-else-if="scope.row.termType == 3">不定期</span>
				</template>
			</el-table-column>
			<el-table-column prop="cardCode" header-align="center" align="center" label="编号">
			</el-table-column>
			<el-table-column prop="cardUrl" header-align="center" align="center" label="图片url">
				<template slot-scope="scope">
					<img :src="scope.row.cardUrl" alt="" style="width: 100px;display: block;margin: 0 auto;">
				</template>
			</el-table-column>
			<el-table-column prop="cardAmount" header-align="center" align="center" label="金额">
			</el-table-column>
			<el-table-column prop="termDays" header-align="center" align="center" label="天数">
			</el-table-column>
			<el-table-column prop="status" header-align="center" align="center" label="状态">
				<template slot-scope="scope">
					<span v-if="scope.row.status == 0">启用</span>
					<span v-else-if="scope.row.status == 1">禁用</span>
				</template>
			</el-table-column>
			<el-table-column prop="note" header-align="center" align="center" label="备注">
			</el-table-column>
		</el-table>
		<div class="btn-warper">
			<el-button type="primary" size="mini" @click="editHandle">修改</el-button>
			<el-button type="danger" size="mini" @click="deleteHandle">删除</el-button>
		</div>
		<el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex" :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage" layout="total, sizes, prev, pager, next, jumper"></el-pagination>

		<el-dialog title="新增" :close-on-click-modal="false" :visible.sync="addvisible" width="760px">
			<el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="160px" class="w-80">
				<el-form-item label="主体类别" size="mini" prop="cardType">
					<el-radio-group v-model="dataForm.cardType">
						<el-radio :label="0">会员</el-radio>
						<el-radio :label="1">商家</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="期限类别" size="mini" prop="termType">
					<el-radio-group v-model="dataForm.termType">
						<el-radio :label="0">年卡</el-radio>
						<el-radio :label="1">季卡</el-radio>
						<el-radio :label="2">月卡</el-radio>
						<el-radio :label="3">不定期限</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="图片url" prop="cardUrl">
					<el-upload :action="uploadUrl" list-type="picture-card" accept="image/*" :limit="1" :file-list="noticePictureImgs" :on-preview="logoPreview" :on-success="handleAvatarSuccess" :before-upload="logorUpload">
						<i class="el-icon-plus"></i>
					</el-upload>
				</el-form-item>
				<el-form-item label="金额" prop="cardAmount">
					<el-input v-model="dataForm.cardAmount" placeholder="金额"></el-input>
				</el-form-item>
				<el-form-item label="天数" prop="termDays">
					<el-input v-model="dataForm.termDays" placeholder="天数"></el-input>
				</el-form-item>
				<el-form-item label="状态" prop="status">
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
			<el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="160px" class="w-80">
				<el-form-item label="主体类别" size="mini" prop="cardType">
					<el-radio-group v-model="dataForm.cardType">
						<el-radio :label="0">会员</el-radio>
						<el-radio :label="1">商家</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="期限类别" size="mini" prop="termType">
					<el-radio-group v-model="dataForm.termType">
						<el-radio :label="0">年卡</el-radio>
						<el-radio :label="1">季卡</el-radio>
						<el-radio :label="2">月卡</el-radio>
						<el-radio :label="3">不定期限</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="编号" prop="cardCode">
					<el-input v-model="dataForm.cardCode" :disabled="true" placeholder="编号"></el-input>
				</el-form-item>
				<el-form-item label="图片url" prop="cardUrl">
					<el-upload :action="uploadUrl" list-type="picture-card" accept="image/*" :limit="1" :file-list="noticePictureImgs" :on-preview="logoPreview" :on-success="handleAvatarSuccess" :before-upload="logorUpload">
						<i class="el-icon-plus"></i>
					</el-upload>
				</el-form-item>
				<el-form-item label="金额" prop="cardAmount">
					<el-input v-model="dataForm.cardAmount" placeholder="金额"></el-input>
				</el-form-item>
				<el-form-item label="天数" prop="termDays">
					<el-input v-model="dataForm.termDays" placeholder="天数"></el-input>
				</el-form-item>
				<el-form-item label="状态" prop="status">
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
	</div>
</template>

<script>
	export default {
		data() {
			return {
				dataList: [],
				condition: {
					cardType: '',
					termType: '',
					status: '',
					page: 1,
					size: 10
				},
				dataForm: {
					uuid: '',
					cardType: '',
					termType: '',
					cardUrl: '',
					cardAmount: '',
					termDays: '',
					status: '',
					note: ''
				},
				dataRule: {
					fullName: [{
						required: true,
						message: '金额不能为空',
						trigger: 'blur'
					}],
					termDays: [{
						required: true,
						message: '天数不能为空',
						trigger: 'blur'
					}]
				},
				uploadUrl: 'http://192.168.1.121:13001/dwj-web-oss/oss/uploadFileToDir?dirName=seller',
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
				cardUrl: '',
				visible: false,
				noticePictureImgs: []
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
				this.condition = {
					cardType: '',
					termType: '',
					status: '',
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
					url: this.$http.adornUrl("/sale/saleCardTicket/list"),
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
					cardType: 0,
					termType: 0,
					cardUrl: '',
					cardAmount: '',
					termDays: '',
					status: 0,
					note: ''
				}
				this.noticePictureImgs = []
				this.addvisible = true
			},

			logoPreview(file) { //预览图片时调用
				this.cardUrl = file.url;
				this.visible = true;
			},
			logorUpload(file) {
				var formData = new FormData();
				formData.append("file", file);
				this.$http.post("http://192.168.1.121:13001/dwj-web-oss/oss/uploadFileToDir?dirName=seller", formData)
					.then(({
						data
					}) => {
						this.dataForm.cardUrl = data.data
					});
			},
			handleAvatarSuccess(file) { //图片上传成功
				console.log("上传成功")
			},

			//新增表单提交
			addDataFormSubmit() {

				this.$refs["dataForm"].validate(valid => {
					if(valid) {
						this.$http.post(this.$http.adornUrl("/sale/saleCardTicket/save"), this.dataForm)
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
				if(this.dataListSelections[0].cardUrl) {
					let img = {
						name: '优惠券图片',
						url: this.dataListSelections[0].cardUrl
					}
					this.noticePictureImgs.push(img)
				}
			},

			//修改表单提交
			dataFormSubmit() {
				this.$refs["dataForm"].validate(valid => {
					if(valid) {
						this.$http.post(this.$http.adornUrl("/sale/saleCardTicket/update"), this.dataForm)
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
						this.$http.post(this.$http.adornUrl("/sale/saleCardTicket/delete"), formData)
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