<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>优惠券批次管理</h4>
			<div class="btns">
				<el-button type="primary" size="mini" @click="addHandle()">新增</el-button>
			</div>
		</div>
		<div class="tool-header">
			<el-form :inline="true" :model="condition" @keyup.enter.native="getDataList()">
				<!--<el-form-item>
					<el-select v-model="condition.createSubjectType" size="mini" clearable placeholder="发布主体">
						<el-option label="平台" value="0"></el-option>
						<el-option label="商家" value="1"></el-option>
						<el-option label="消费者" value="2"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.paySubjectType" size="mini" clearable placeholder="支付主体">
						<el-option label="平台" value="0"></el-option>
						<el-option label="商家" value="1"></el-option>
						<el-option label="消费者" value="2"></el-option>
					</el-select>
				</el-form-item>-->
				<!--<el-form-item>
					<el-select v-model="condition.sellerCode" clearable placeholder="商家名称" clearable class="w-100">
						<el-option v-for="(item,index) in allsellers" :key="index" :label="item.name" :value="item.value"></el-option>
					</el-select>
				</el-form-item>-->
				<el-form-item>
					<el-input v-model="condition.batchName" placeholder="批次名称" size="mini" class="w120"></el-input>
				</el-form-item>
				<!--<el-form-item>
					<el-select v-model="condition.publishType" size="mini" clearable placeholder="发放方式">
						<el-option label="线上" value="0"></el-option>
						<el-option label="线下" value="1"></el-option>
					</el-select>
				</el-form-item>-->
				<!--<el-form-item>
					<el-select v-model="condition.reflectStatus" size="mini" clearable placeholder="提现状态">
						<el-option label="不能提现" value="0"></el-option>
						<el-option label="可提现" value="1"></el-option>
					</el-select>
				</el-form-item>-->
				<el-form-item>
					<el-select v-model="condition.effectStatus" size="mini" clearable placeholder="有效状态">
						<el-option label="有效" value="0"></el-option>
						<el-option label="无效" value="1"></el-option>
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
			<!--<el-table-column prop="industryTypeName" header-align="center" align="center" label="行业类别">
			</el-table-column>-->
			<!--<el-table-column prop="createSubjectType" header-align="center" align="center" label="发放主体">
				<template slot-scope="scope">
					<span v-if="scope.row.createSubjectType == 0">平台</span>
					<span v-else-if="scope.row.createSubjectType == 1">商家</span>
				</template>
			</el-table-column>-->
			<!--<el-table-column prop="paySubjectType" header-align="center" align="center" label="支付主体">
				<template slot-scope="scope">
					<span v-if="scope.row.paySubjectType == 0">平台</span>
					<span v-else-if="scope.row.paySubjectType == 1">商家</span>
				</template>
			</el-table-column>-->
			<!--<el-table-column prop="sellerCode" header-align="center" align="center" label="商家编码">
			</el-table-column>
			<el-table-column prop="shortName" header-align="center" align="center" label="商家名称">
			</el-table-column>-->
			<el-table-column prop="batchCode" header-align="center" align="center" label="批次编码">
			</el-table-column>
			<el-table-column prop="batchName" header-align="center" align="center" label="批次名称">
			</el-table-column>
			<el-table-column prop="deductionAmount" header-align="center" align="center" label="抵扣金额">
			</el-table-column>
			<el-table-column prop="limitLowerAmount" header-align="center" align="center" label="最低限额">
			</el-table-column>
			<el-table-column prop="limitTimeStart" header-align="center" align="center" label="开始时间">
				<template slot-scope="scope">
					<span v-if="scope.row.limitTimeStart == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.limitTimeStart).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.limitTimeStart).toTimeString().substr(0, 8)}}</span>
					</span>
				</template>
			</el-table-column>
			<el-table-column prop="limitTimeEnd" header-align="center" align="center" label="截止时间">
				<template slot-scope="scope">
					<span v-if="scope.row.limitTimeEnd == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.limitTimeEnd).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.limitTimeEnd).toTimeString().substr(0, 8)}}</span>
					</span>
				</template>
			</el-table-column>
			<!--<el-table-column prop="batchSize" header-align="center" align="center" label="发放数量">
			</el-table-column>
			<el-table-column prop="realSize" header-align="center" align="center" label="实时数量">
			</el-table-column>-->
			<!--<el-table-column prop="publishType" header-align="center" align="center" label="发放方式">
			</el-table-column>-->
			<!--<el-table-column prop="reflectStatus" header-align="center" align="center" label="提现状态">
			</el-table-column>-->
			<el-table-column prop="effectStatus" header-align="center" align="center" label="有效状态">
				<template slot-scope="scope">
					<span v-if="scope.row.effectStatus == 0">有效</span>
					<span v-else="scope.row.effectStatus == 1">无效</span>
				</template>
			</el-table-column>
			<el-table-column prop="status" header-align="center" align="center" label="状态">
				<template slot-scope="scope">
					<span v-if="scope.row.status == 0">启用</span>
					<span v-else="scope.row.status == 1">禁用</span>
				</template>
			</el-table-column>
		</el-table>
		<div class="btn-warper">
			<el-button type="primary" size="mini" @click="editHandle">修改</el-button>
			<el-button type="danger" size="mini" @click="deleteHandle">删除</el-button>
		</div>
		<el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex" :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage" layout="total, sizes, prev, pager, next, jumper"></el-pagination>

		<el-dialog title="新增" :close-on-click-modal="false" :visible.sync="addvisible" width="760px">
			<el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="addDataFormSubmit()" label-width="160px" class="w-80">
				<el-form-item label="批次名称" prop="batchName">
					<el-input v-model="dataForm.batchName" placeholder="批次名称"></el-input>
				</el-form-item>

				<el-form-item label="抵扣金额" prop="deductionAmount">
					<el-input v-model="dataForm.deductionAmount" placeholder="抵扣金额"></el-input>
				</el-form-item>

				<el-form-item label="最低限额" prop="limitLowerAmount">
					<el-input v-model="dataForm.limitLowerAmount" placeholder="最低限额"></el-input>
				</el-form-item>

				<el-form-item label="起止时间" prop="supplyTime">
					<el-date-picker style="width: 260px" v-model="dataForm.supplyTime" size="mini" type="daterange" value-format="yyyy-MM-dd" range-separator="-" start-placeholder="开始日期" end-placeholder="结束日期">
					</el-date-picker>
				</el-form-item>

				<el-form-item label="有效状态" size="mini" prop="effectStatus">
					<el-radio-group v-model="dataForm.effectStatus">
						<el-radio :label="0">有效</el-radio>
						<el-radio :label="1">无效</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="优惠券图片" prop="ruleNoteUrl">
					<el-upload :action="uploadUrl" list-type="picture-card" accept="image/*" :limit="1" :file-list="noticePictureImgs" :on-preview="logoPreview" :on-success="handleAvatarSuccess" :before-upload="logorUpload">
						<i class="el-icon-plus"></i>
					</el-upload>
				</el-form-item>

				<el-form-item label="规则描述" prop="ruleNote">
					<el-input type="textarea" v-model="dataForm.ruleNote" placeholder="名称"></el-input>
				</el-form-item>
				<el-form-item label="状态" size="mini" prop="status">
					<el-radio-group v-model="dataForm.status">
						<el-radio :label="0">启用</el-radio>
						<el-radio :label="1">禁用</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="备注" prop="note">
					<el-input type="textarea" v-model="dataForm.note" placeholder="备注"></el-input>
				</el-form-item>
			</el-form>
			<span slot="footer" class="dialog-footer">
        		<el-button @click="addvisible = false">取消</el-button>
        		<el-button type="primary" @click="addDataFormSubmit()">确定</el-button>
      		</span>
		</el-dialog>

		<el-dialog title="修改" :close-on-click-modal="false" :visible.sync="editvisible" width="760px">
			<el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="160px" class="w-80">
				<el-form-item label="批次编码" prop="batchCode">
					<el-input v-model="dataForm.batchCode" :disabled='true' placeholder="批次编码"></el-input>
				</el-form-item>
				<el-form-item label="批次名称" prop="batchName">
					<el-input v-model="dataForm.batchName" placeholder="批次名称"></el-input>
				</el-form-item>

				<el-form-item label="抵扣金额" prop="deductionAmount">
					<el-input v-model="dataForm.deductionAmount" placeholder="抵扣金额"></el-input>
				</el-form-item>

				<el-form-item label="最低限额" prop="limitLowerAmount">
					<el-input v-model="dataForm.limitLowerAmount" placeholder="最低限额"></el-input>
				</el-form-item>

				<el-form-item label="起止时间" prop="supplyTime">
					<el-date-picker style="width: 260px" v-model="dataForm.supplyTime" size="mini" type="daterange" value-format="yyyy-MM-dd" range-separator="-" start-placeholder="开始日期" end-placeholder="结束日期">
					</el-date-picker>
				</el-form-item>

				<el-form-item label="有效状态" size="mini" prop="effectStatus">
					<el-radio-group v-model="dataForm.effectStatus">
						<el-radio :label="0">有效</el-radio>
						<el-radio :label="1">无效</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="优惠券图片" prop="ruleNoteUrl">
					<el-upload :action="uploadUrl" list-type="picture-card" accept="image/*" :limit="1" :file-list="noticePictureImgs" :on-preview="logoPreview" :on-success="handleAvatarSuccess" :before-upload="logorUpload">
						<i class="el-icon-plus"></i>
					</el-upload>
				</el-form-item>

				<el-form-item label="规则描述" prop="ruleNote">
					<el-input type="textarea" v-model="dataForm.ruleNote" placeholder="名称"></el-input>
				</el-form-item>
				<el-form-item label="状态" size="mini" prop="status">
					<el-radio-group v-model="dataForm.status">
						<el-radio :label="0">启用</el-radio>
						<el-radio :label="1">禁用</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="备注" prop="note">
					<el-input type="textarea" v-model="dataForm.note" placeholder="备注"></el-input>
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
					industryTypeCode: '',
					createSubjectType: '',
					paySubjectType: '',
					sellerCode: '',
					batchName: '',
					publishType: '',
					reflectStatus: '',
					effectStatus: '',
					status: '',
					page: 1,
					size: 10
				},
				dataForm: {
					uuid: '',
					createSubjectType: '0',
					paySubjectType: '0',
					sellerCode: '',
					batchName: '',
					deductionAmount: '',
					supplyTime: [],
					limitTimeStart: 0,
					limitTimeEnd: 0,
					batchSize: -1,
					realSize: -1,
					publishType: '0',
					effectStatus: '',
					reflectStatus: '0',
					ruleNoteUrl: '',
					ruleNote: '',
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
					batchCode: [{
						required: true,
						message: '批次编码不能为空',
						trigger: 'change'
					}],
					batchName: [{
						required: true,
						message: '批次名称不能为空',
						trigger: 'change'
					}],
					deductionAmount: [{
						required: true,
						message: '抵扣金额不能为空',
						trigger: 'blur'
					}],
					最低限额: [{
						required: true,
						message: '最低限额不能为空',
						trigger: 'blur'
					}],
					batchSize: [{
						required: true,
						message: '发放数量不能为空',
						trigger: 'blur'
					}],
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
				ruleNoteUrl: '',
				visible: false,
				noticePictureImgs: [],
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
					industryTypeCode: '',
					createSubjectType: '',
					paySubjectType: '',
					sellerCode: '',
					batchName: '',
					publishType: '',
					reflectStatus: '',
					effectStatus: '',
					status: '',
					page: 1,
					size: 10
				}
			},
//			//显示所有商家
//			showSeller() {
//				this.$http({
//					url: this.$http.adornUrl('/sale/saleSeller/querySaleSeller'),
//					method: "post",
//					params: this.$http.adornParams()
//				}).then(({
//					data
//				}) => {
//					this.allsellers = data.data
//				});
//			},
//			getTypes() {
//				this.$http({
//					url: this.$http.adornUrl("/sale/saleDict/findByParentCode"),
//					method: "get",
//					params: this.$http.adornParams({
//						parentCode: 'industry'
//					})
//				}).then(({
//					data
//				}) => {
//					this.types = data.dataList
//				});
//			},
			// 获取数据列表
			getDataList() {
				this.dataListLoading = true;
				this.condition.page = this.pageIndex
				this.condition.size = this.pageSize
				this.$http({
					url: this.$http.adornUrl("/sale/saleCouponBatch/list"),
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
					createSubjectType: 0,
					paySubjectType: 0,
					sellerCode: '',
					batchCode: '',
					batchName: '',
					deductionAmount: '',
					supplyTime: [],
					limitTimeStart: 0,
					limitTimeEnd: '',
					batchSize: '',
					realSize: '',
					publishType: 0,
					effectStatus: 0,
					reflectStatus: 0,
					ruleNoteUrl: '',
					ruleNote: '',
					status: 0,
					note: ''
				}
				this.addvisible = true
			},

			logoPreview(file) { //预览图片时调用
				this.ruleNoteUrl = file.url;
				this.visible = true;
			},
			logorUpload(file) {
				var formData = new FormData();
				formData.append("file", file);
				this.$http.post("http://192.168.1.121:13001/dwj-web-oss/oss/uploadFileToDir?dirName=seller", formData)
					.then(({
						data
					}) => {
						this.dataForm.ruleNoteUrl = data.data
					});
			},
			handleAvatarSuccess(file) { //图片上传成功
				console.log("上传成功")
			},

			//新增表单提交
			addDataFormSubmit() {
				this.dataForm.limitTimeStart = new Date(this.dataForm.supplyTime[0]).getTime()
				this.dataForm.limitTimeEnd = new Date(this.dataForm.supplyTime[1]).getTime()
				this.$refs["dataForm"].validate(valid => {
					if(valid) {
						this.$http.post(this.$http.adornUrl("/sale/saleCouponBatch/save"), this.dataForm)
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
				this.dataForm.supplyTime = [new Date(this.dataForm.limitTimeStart),new Date(this.dataForm.limitTimeEnd)]
				
				console.log(this.dataForm)
			},
			//修改表单提交
			dataFormSubmit() {

				this.dataForm.limitTimeStart = new Date(this.dataForm.supplyTime[0]).getTime()
				this.dataForm.limitTimeEnd = new Date(this.dataForm.supplyTime[1]).getTime()

				this.$refs["dataForm"].validate(valid => {
					if(valid) {
						this.$http.post(this.$http.adornUrl("/sale/saleCouponBatch/update"), this.dataForm)
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
						this.$http.post(this.$http.adornUrl("/sale/saleCouponBatch/delete"), formData)
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