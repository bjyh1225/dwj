<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>商品类别</h4>
			<div class="btns">
				<el-button type="primary" size="mini" @click="addHandle()">新增</el-button>
			</div>
		</div>
		<el-form :inline="true" :model="condition">
			<el-form-item>
				<el-select size="mini" v-model="condition.industryTypeCode" placeholder="行业类别" clearable class="w-100" @change="industryChange">
					<el-option v-for="item in types" :key="item.value" :label="item.label" :value="item.value"></el-option>
				</el-select>
			</el-form-item>
			<el-form-item>
				<el-select v-model="condition.platformTypeCode" size="mini" clearable placeholder="平台商品类别">
					<el-option v-for="item in platforms" :key="item.value" :label="item.label" :value="item.value"></el-option>
				</el-select>
			</el-form-item>
			<el-form-item>
				<el-select v-model="condition.sellerCode" size="mini" clearable placeholder="商家名称" clearable class="w-100">
					<el-option v-for="item in sellers" :key="item.value" :label="item.name" :value="item.value"></el-option>
				</el-select>
			</el-form-item>
			<el-form-item>
				<el-input v-model="condition.goodsTypeName" placeholder="商品类别名称" clearable size="mini"></el-input>
			</el-form-item>
			<el-form-item>
				<el-button @click="searchHndel()" size="mini" type="primary">查询</el-button>
			</el-form-item>
			<el-form-item>
				<el-button @click="searchReset" size="mini" type="primary">重置</el-button>
			</el-form-item>
		</el-form>
		<el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle" ref="multipleTable" style="width: 100%;">
			<el-table-column type="selection" header-align="center" align="center" width="50">
			</el-table-column>
			<el-table-column type="index" fixed label="序号" width="50" header-align="center" align="center" :index="indexMethod">
			</el-table-column>
			<el-table-column prop="uuid" header-align="center" align="center" label="UUID">
			</el-table-column>
			<el-table-column prop="industryTypeCode" header-align="center" align="center" label="行业类别编码">
			</el-table-column>
			<el-table-column prop="industryTypeName" header-align="center" align="center" label="行业类别名称">
			</el-table-column>
			<el-table-column prop="platformTypeCode" header-align="center" align="center" label="平台商品类别编码">
			</el-table-column>
			<el-table-column prop="platformTypeName" header-align="center" align="center" label="平台商品类别名称">
			</el-table-column>
			<el-table-column prop="platformTypeDiscountScope" header-align="center" align="center" label="平台商品折扣范围">
			</el-table-column>
			<el-table-column prop="goodsTypeDiscount" header-align="center" align="center" label="商品类别折扣">
			</el-table-column>
			<el-table-column prop="sellerCode" header-align="center" align="center" label="商家编码">
			</el-table-column>
			<el-table-column prop="sellerName" header-align="center" align="center" label="商家名称">
			</el-table-column>
			<el-table-column prop="typeNo" header-align="center" align="center" label="类别排序">
			</el-table-column>
			<el-table-column prop="goodsTypeCode" header-align="center" align="center" label="商品类别编码">
			</el-table-column>
			<el-table-column prop="goodsTypeName" header-align="center" align="center" label="商品类别名称">
			</el-table-column>
			<el-table-column prop="listStatus" header-align="center" align="center" label="上架状态">
				<template slot-scope="scope">
					<span v-if="scope.row.listStatus == 0">未上架</span>
					<span v-else-if="scope.row.listStatus == 1">已上架</span>
				</template>
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
			<el-table-column prop="createTime" header-align="center" align="center" label="创建时间">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.createTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.createTime).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="updateUserType" header-align="center" align="center" label="更新人">
				<template slot-scope="scope">
					<span v-if="scope.row.updateUserType == 0">平台</span>
					<span v-else-if="scope.row.updateUserType == 1">商家</span>
					<span v-else-if="scope.row.updateUserType == 2">消费者</span>
				</template>
			</el-table-column>
			<el-table-column prop="updateTime" header-align="center" align="center" label="更新时间">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.updateTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.updateTime).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
		</el-table>
		<div class="btn-warper">
			<el-button type="primary" size="mini" @click="editHandle">修改</el-button>
			<el-button type="danger" size="mini" @click="deleteHandle">删除</el-button>
		</div>
		<el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex" :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage" layout="total, sizes, prev, pager, next, jumper"></el-pagination>

		<el-dialog title="新增" :close-on-click-modal="false" :visible.sync="addvisible" width="560px">
			<el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="140px" class="w-80">
				<el-form-item label="行业类别" prop="industryTypeCode">
					<el-select v-model="dataForm.industryTypeCode" placeholder="行业类别" size="mini" clearable class="w-100" @change="industryChange">
						<el-option v-for="item in types" :key="item.value" :label="item.label" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item label="平台商品类别" prop="platformTypeCode">
					<el-select v-model="dataForm.platformTypeCode" clearable placeholder="请选择" size="mini" class="w-100" @change="platformChange">
						<el-option v-for="item in platforms" :key="item.value" :label="item.label" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item label="平台商品折扣范围" prop="platformTypeDiscountScope">
					<el-input v-model="dataForm.platformTypeDiscountScope" size="mini" :disabled="true"></el-input>
				</el-form-item>
				<el-form-item label="类别序号" prop="typeNo">
					<el-input v-model="dataForm.typeNo" placeholder="类别序号" size="mini" onkeyup="value=value.toUpperCase().replace(/[^\d]/g,'')"></el-input>
				</el-form-item>
				<el-form-item label="商品类别折扣" prop="goodsTypeDiscount">
					<el-select v-model="dataForm.goodsTypeDiscount" placeholder="商品类别折扣" clearable size="mini" class="w-100">
						<el-option v-for="item in discounts" :key="item.value" :label="item.label" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item label="商家" prop="sellerCode">
					<el-select v-model="dataForm.sellerCode" size="mini" clearable placeholder="商家名称" clearable class="w-100">
						<el-option v-for="item in sellers" :key="item.value" :label="item.name" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item label="商品类别名称" prop="goodsTypeName">
					<el-input v-model="dataForm.goodsTypeName" size="mini" placeholder="商品类别名称"></el-input>
				</el-form-item>
				<el-form-item label="上架状态" prop="listStatus">
					<el-radio-group v-model="dataForm.listStatus" size="mini">
						<el-radio :label="0">未上架</el-radio>
						<el-radio :label="1">已上架</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="状态" prop="status" size="mini">
					<el-radio-group v-model="dataForm.status">
						<el-radio :label="0">启用</el-radio>
						<el-radio :label="1">禁用</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="备注" prop="note">
					<el-input v-model="dataForm.note" type="textarea" size="mini" placeholder="备注"></el-input>
				</el-form-item>
			</el-form>
			<span slot="footer" class="dialog-footer">
        <el-button @click="addvisible = false">取消</el-button>
        <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
      </span>
		</el-dialog>

		<el-dialog title="修改" :close-on-click-modal="false" :visible.sync="editvisible" width="560px">
			<el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="140px" class="w-80">
				<el-form-item label="行业类别" prop="industryTypeCode">
					<el-select v-model="dataForm.industryTypeCode" placeholder="行业类别" clearable size="mini" class="w-100" @change="industryChange">
						<el-option v-for="item in types" :key="item.value" :label="item.label" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item label="平台商品类别" prop="platformTypeCode">
					<el-select v-model="dataForm.platformTypeCode" clearable placeholder="请选择" size="mini" class="w-100" @change="platformChange">
						<el-option v-for="item in platforms" :key="item.value" :label="item.label" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item label="平台商品折扣范围" prop="platformTypeDiscountScope">
					<el-input v-model="dataForm.platformTypeDiscountScope" placeholder="平台商品折扣范围" size="mini" :disabled="true"></el-input>
				</el-form-item>
				<el-form-item label="类别序号" prop="typeNo">
					<el-input v-model="dataForm.typeNo" placeholder="类别序号" size="mini" onkeyup="value=value.toUpperCase().replace(/[^\d]/g,'')"></el-input>
				</el-form-item>
				<el-form-item label="商品类别折扣" prop="goodsTypeDiscount">
					<el-select v-model="dataForm.goodsTypeDiscount" placeholder="商品类别折扣" size="mini" clearable class="w-100">
						<el-option v-for="item in discounts" :key="item.value" :label="item.label" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item label="商家" prop="sellerCode">
					<el-select v-model="dataForm.sellerCode" size="mini" clearable placeholder="商家名称" clearable class="w-100">
						<el-option v-for="item in sellers" :key="item.value" :label="item.name" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item label="商品类别编码" prop="goodsTypeCode">
					<el-input v-model="dataForm.goodsTypeCode" placeholder="商品类别编码" size="mini" :disabled="true"></el-input>
				</el-form-item>
				<el-form-item label="商品类别名称" prop="goodsTypeName">
					<el-input v-model="dataForm.goodsTypeName" size="mini" placeholder="商品类别名称"></el-input>
				</el-form-item>
				<el-form-item label="上架状态" prop="listStatus">
					<el-radio-group v-model="dataForm.listStatus" size="mini">
						<el-radio :label="0">未上架</el-radio>
						<el-radio :label="1">已上架</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="状态" prop="status">
					<el-radio-group v-model="dataForm.status" size="mini">
						<el-radio :label="0">启用</el-radio>
						<el-radio :label="1">禁用</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="备注" prop="note">
					<el-input v-model="dataForm.note" type="textarea" size="mini" placeholder="备注"></el-input>
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
				condition: {
					goodsTypeName: '',
					industryTypeCode: '',
					sellerCode: '',
					platformTypeCode: '',
				},
				dataList: [],
				pageIndex: 1,
				pageSize: 10,
				currentPage: 1,
				totalPage: 0,
				dataListLoading: false,
				dataListSelections: [],
				addOrUpdateVisible: false,
				addvisible: false,
				editvisible: false,
				types: [],
				sellers: [],
				platforms: [],
				discounts: [],
				dataForm: {
					industryTypeName: '',
					platformTypeCode: '',
					platformTypeDiscountScope: '',
					goodsTypeDiscount: '',
					sellerCode: '',
					typeNo: '',
					goodsTypeCode: '',
					goodsTypeName: '',
					listStatus: 0,
					note: '',
					status: 0,
				},
				flag: false,
				emptyDate: {},
				dataRule: {
					industryTypeCode: [{
						required: true,
						message: '行业类别不能为空',
						trigger: 'change'
					}],
					platformTypeCode: [{
						required: true,
						message: '平台商品类别不能为空',
						trigger: 'change'
					}],
					goodsTypeDiscount: [{
						required: true,
						message: '商品类别折扣不能为空',
						trigger: 'change'
					}],
					sellerCode: [{
						required: true,
						message: '商家不能为空',
						trigger: 'change'
					}],
					goodsTypeCode: [{
						required: true,
						message: '商品类别编码不能为空',
						trigger: 'blur'
					}],
					goodsTypeName: [{
						required: true,
						message: '商品类别名称不能为空',
						trigger: 'blur'
					}]
					
				}
			};
		},
		activated() {
			this.getDataList();
			this.getTypes()
			this.emptyDate = this.dataForm
		},
		methods: {
			searchHndel() {
				this.pageIndex = 1
				this.pageSize = 10
				this.getDataList()
			},
			searchReset() {
				this.condition = {
					goodsTypeName: '',
					industryTypeCode: '',
					sellerCode: '',
					platformTypeCode: ''
				}
			},
			//获取行业列别
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
			
			industryChange(val) {
				
				this.condition.platformTypeCode = ''
				this.condition.sellerCode = ''
				
				//获取商家
				this.$http({
					url: this.$http.adornUrl("/sale/saleSeller/findByIndustryTypeCode"),
					method: "post",
					params: this.$http.adornParams({
						industryTypeCode: val
					})
				}).then(({
					data
				}) => {
					this.sellers = data.data
				});
				//获取平台类别
				this.$http({
					url: this.$http.adornUrl("/sale/saleDict/findByParentCode"),
					method: "get",
					params: this.$http.adornParams({
						parentCode: val
					})
				}).then(({
					data
				}) => {
					this.platforms = data.dataList
					this.dataForm.platformTypeCode = data.dataList[0].value
					this.platformChange(this.dataForm.platformTypeCode)
				});
				
			},
			//获取平台商品类别折扣范围
			platformChange(val) {
				this.$http({
					url: this.$http.adornUrl("/sale/saleDict/findDiscountScope"),
					method: "get",
					params: this.$http.adornParams({
						code: val
					})
				}).then(({
					data
				}) => {
					this.dataForm.platformTypeDiscountScope = ''
					data.dataList.forEach(item => {
						this.dataForm.platformTypeDiscountScope = this.dataForm.platformTypeDiscountScope + ','+ item.value
					})
					this.dataForm.platformTypeDiscountScope = this.dataForm.platformTypeDiscountScope.substr(1)
					this.discounts = data.dataList
					
					let flag = false
					for(var i=0; i<this.discounts.length;i++){
						if(this.dataForm.goodsTypeDiscount == Number(this.discounts[i].value)){
							flag = true
						}
					}
					if(flag == false){
						this.dataForm.goodsTypeDiscount = ''
					}
					
				});
			},

			// 获取数据列表
			getDataList() {
				this.dataListLoading = true;
				this.$http({
					url: this.$http.adornUrl("/sale/saleGoodsType/list"),
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
				this.addvisible = true;
				if(this.$refs.dataForm) {
					this.$refs.dataForm.resetFields();
				}
				this.dataForm = this.emptyDate
				delete this.dataForm.uuid;
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
				this.editvisible = true;
				//获取内容
				let id = this.dataListSelections[0].uuid
				this.$http({
					url: this.$http.adornUrl(`/sale/saleGoodsType/info/${id}`),
					method: "get",
					params: this.$http.adornParams()
				}).then(({
					data
				}) => {
					this.industryChange(data.data.industryTypeCode)
					this.platformChange(data.data.platformTypeCode)
					this.dataForm = data.data;
				});

			},

			//表单提交
			dataFormSubmit() {
				this.$refs["dataForm"].validate(valid => {
					if(valid) {
						this.$http({
							url: this.$http.adornUrl(
								`/sale/saleGoodsType/${!this.dataForm.uuid ? "save" : "update"}`
							),
							method: "post",
							data: this.$http.adornData(this.dataForm)
						}).then(({
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
						this.$http.post(this.$http.adornUrl("/sale/saleGoodsType/delete"), formData)
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

<style scoped>
	.mod-home {
		line-height: 1.5;
	}
</style>