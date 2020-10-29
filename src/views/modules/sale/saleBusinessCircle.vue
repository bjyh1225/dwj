<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>商圈管理</h4>
			<div class="btns">
				<el-button type="primary" size="mini" @click="addHandle()">新增</el-button>
			</div>
		</div>
		<div class="tool-header">
			<el-form :inline="true" :model="condition" @keyup.enter.native="getDataList()">
				<el-form-item>
					<el-cascader size="mini" v-model="condition.address1" :options="AddressList" placeholder="区域" expand-trigger="hover" :clearable="true" :change-on-select="true"></el-cascader>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.circleName" placeholder="商圈名称" clearable size="mini"></el-input>
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
			<el-table-column prop="province" header-align="center" align="center" label="省编码">
			</el-table-column>
			<el-table-column prop="provinceName" header-align="center" align="center" label="省">
			</el-table-column>
			<el-table-column prop="city" header-align="center" align="center" label="市编码">
			</el-table-column>
			<el-table-column prop="cityName" header-align="center" align="center" label="市">
			</el-table-column>
			<el-table-column prop="area" header-align="center" align="center" label="区编码">
			</el-table-column>
			<el-table-column prop="areaName" header-align="center" align="center" label="区">
			</el-table-column>
			<el-table-column prop="circleCode" header-align="center" align="center" label="商圈编码">
			</el-table-column>
			<el-table-column prop="circleName" header-align="center" align="center" label="商圈名称">
			</el-table-column>
			<el-table-column prop="circleLongitude" header-align="center" align="center" label="经度">
			</el-table-column>
			<el-table-column prop="circleLatitude" header-align="center" align="center" label="纬度">
			</el-table-column>
			<el-table-column prop="status" header-align="center" align="center" label="状态">
			</el-table-column>
		</el-table>
		<div class="btn-warper">
			<el-button type="primary" size="mini" @click="editHandle">修改</el-button>
			<el-button type="danger" size="mini" @click="deleteHandle">删除</el-button>
			
		</div>
		<el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex" :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage" layout="total, sizes, prev, pager, next, jumper"></el-pagination>

		<el-dialog title="新增" :close-on-click-modal="false" :visible.sync="addvisible" width="520px">
			<el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="addDataFormSubmit()" label-width="100px" class="w-80">
				<el-form-item label="区域">
					<el-cascader size="mini" v-model="region" :options="AddressList" @change="handleChange" expand-trigger="hover" placeholder="区域" :clearable="true" class="w-100"></el-cascader>
				</el-form-item>
				<el-form-item label="经度" prop="circleLongitude">
					<el-input size="mini" v-model="dataForm.circleLongitude" placeholder="经度"></el-input>
				</el-form-item>
				<el-form-item label="纬度" prop="circleLatitude">
					<el-input size="mini" v-model="dataForm.circleLatitude" placeholder="纬度"></el-input>
				</el-form-item>
				<el-form-item label="商圈名称" prop="circleName">
					<el-input size="mini" v-model="dataForm.circleName" placeholder="商圈名称"></el-input>
				</el-form-item>
				<el-form-item label="状态" prop="status">
					<el-radio-group v-model="dataForm.status">
						<el-radio :label="0">启用</el-radio>
						<el-radio :label="1">禁用</el-radio>
					</el-radio-group>
				</el-form-item>
			</el-form>
			<span slot="footer" class="dialog-footer">
        		<el-button @click="addvisible = false">取消</el-button>
        		<el-button type="primary" @click="addDataFormSubmit()">确定</el-button>
      		</span>
		</el-dialog>
		
		<el-dialog title="修改" :close-on-click-modal="false" :visible.sync="editvisible" width="520px">
			<el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="100px" class="w-80">
				<el-form-item label="区域">
					<el-cascader size="mini" v-model="region" :options="AddressList" @change="handleChange" expand-trigger="hover" placeholder="区域" :clearable="true" class="w-100"></el-cascader>
				</el-form-item>
				<el-form-item label="经度" prop="circleLongitude">
					<el-input size="mini" v-model="dataForm.circleLongitude" placeholder="经度"></el-input>
				</el-form-item>
				<el-form-item label="纬度" prop="circleLatitude">
					<el-input size="mini" v-model="dataForm.circleLatitude" placeholder="纬度"></el-input>
				</el-form-item>
				<el-form-item label="商圈名称" prop="circleName">
					<el-input size="mini" v-model="dataForm.circleName" placeholder="商圈名称"></el-input>
				</el-form-item>
				<el-form-item label="状态" prop="status">
					<el-radio-group v-model="dataForm.status">
						<el-radio :label="0">启用</el-radio>
						<el-radio :label="1">禁用</el-radio>
					</el-radio-group>
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
	import jsondata from '../../../../static/json/provinces.json'
	export default {
		data() {
			return {
				dataList: [],
				condition: {
					province:'',
					city: '',
					area: '',
					address1: [],
					circleName: '',
				},
				dataForm: {
					province: '',
					city: '',
					area: '',
					provinceName: '',
					cityName: '',
					areaName: '',
					circleLongitude: '',
					circleLatitude: '',
					circleName: '',
					status: 1,
				},
				dataRule: {
					circleLongitude: [{
						required: true,
						message: '经度不能为空',
						trigger: 'blur'
					},{
						pattern: /^(?!(0[0-9]{0,}$))[0-9]{1,}[.]{0,}[0-9]{0,}$/,
						message: '经度格式错误'
					}],
					circleLatitude: [{
						required: true,
						message: '纬度不能为空',
						trigger: 'blur'
					},{
						pattern: /^(?!(0[0-9]{0,}$))[0-9]{1,}[.]{0,}[0-9]{0,}$/,
						message: '纬度格式错误'
					}],
					circleName: [{
						required: true,
						message: '商圈名称不能为空',
						trigger: 'blur'
					}]
				},
				region: [],
				AddressList: [],
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false,
				addvisible: false,
				editvisible: false
			};
		},
		activated() {
			this.AddressList = jsondata
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
					province:'',
					city: '',
					area: '',
					address: [],
					circleName: '',
				}
			},

			// 获取数据列表
			getDataList() {
				this.dataListLoading = true;
				this.condition.page = this.pageIndex
				this.condition.size = this.pageSize
				if(this.condition.address1.length != 0){
					this.condition.province = this.condition.address1[0]
					this.condition.city = this.condition.address1[1]
					this.condition.area = this.condition.address1[2]
				}
				console.log(this.condition)
				
				this.$http({
					url: this.$http.adornUrl("/sale/saleBusinessCircle/list"),
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
			getCascaderObj(val, opt) {
				return val.map(function(value, index, array) {
					for(var itm of opt) {
						if(itm.value == value) {
							opt = itm.children;
							return itm;
						}
					}
					return null;
				});
			},

			handleChange(value) {
				let vals= this.getCascaderObj(value, this.AddressList)
				
				this.dataForm.province = vals[0].value
				this.dataForm.provinceName = vals[0].label
				
				this.dataForm.city = vals[1].value
				this.dataForm.cityName = vals[1].label
				
				this.dataForm.area = vals[2].value
				this.dataForm.areaName = vals[2].label
				
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
			addHandle() {
				if(this.$refs.dataForm) {
					this.$refs.dataForm.resetFields();
				}
				
				this.dataForm.circleLongitude = ''
				this.dataForm.circleLatitude = ''
				this.dataForm.circleName = ''
				this.dataForm.status = 1
				this.addvisible = true
			},
			editHandle(){
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
				this.region = [this.dataForm.province,this.dataForm.city,this.dataForm.area]
			},
			addDataFormSubmit() {
				this.$refs["dataForm"].validate(valid => {
					if(valid) {
						this.$http.post(this.$http.adornUrl("/sale/saleBusinessCircle/save"), this.dataForm)
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
			dataFormSubmit(){
				this.$refs["dataForm"].validate(valid => {
					if(valid) {
						this.$http.post(this.$http.adornUrl("/sale/saleBusinessCircle/update"), this.dataForm)
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
						this.$http.post(this.$http.adornUrl("/sale/saleBusinessCircle/delete"), formData)
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