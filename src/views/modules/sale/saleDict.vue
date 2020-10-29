<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>字典管理</h4>
			<div class="btns">
				<el-button type="primary" size="mini" @click="addHandle()">新增</el-button>
			</div>
		</div>
		<el-form :inline="true" :model="condition" @keyup.enter.native="getDataList()">
			<el-form-item>
				<el-input v-model="condition.name" placeholder="请输入名称" clearable size="mini"></el-input>
			</el-form-item>
			<el-form-item>
				<el-button @click="searchHndel()" type="primary" size="mini">查询</el-button>
			</el-form-item>
			<el-form-item>
				<el-button @click="searchReset" size="mini" type="primary">重置</el-button>
			</el-form-item>
		</el-form>
		<el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle" ref="multipleTable" style="width: 100%;">
			<el-table-column type="selection" header-align="center" align="center"></el-table-column>
			<el-table-column prop="id" header-align="center" align="center" width="80" label="ID"></el-table-column>
			<el-table-column prop="parentId" header-align="center" align="center" width="80" label="父ID"></el-table-column>
			<el-table-column prop="code" header-align="center" align="center" label="编码"></el-table-column>
			<el-table-column prop="name" header-align="center" align="center" label="名称"></el-table-column>
			<el-table-column prop="sort" header-align="center" align="center" width="80" label="序号"></el-table-column>
			<el-table-column prop="note" header-align="center" align="center" label="备注"></el-table-column>
			<el-table-column prop="status" header-align="center" align="center" label="状态">
				<template slot-scope="scope">
					<el-tag v-if="scope.row.status === 0" size="small">正常</el-tag>
					<el-tag v-else size="small" type="danger">禁用</el-tag>
				</template>
			</el-table-column>
			<el-table-column prop="ext1" header-align="center" align="center" label="扩展字段1"></el-table-column>
			<el-table-column prop="ext2" header-align="center" align="center" label="扩展字段2"></el-table-column>
			<el-table-column prop="ext3" header-align="center" align="center" label="扩展字段3"></el-table-column>
			<el-table-column prop="createUser" header-align="center" align="center" label="创建人"></el-table-column>
			<el-table-column prop="createTime" header-align="center" align="center" label="创建时间">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.createTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.createTime).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
				
			<el-table-column prop="updateUser" header-align="center" align="center" label="更新人"></el-table-column>
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

		<el-dialog title="新增" :close-on-click-modal="false" :visible.sync="addvisible" width="760px">
			<el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="160px" class="w-80">
				<el-form-item label="父ID" prop="parentId">
					<el-input v-model="parentIdname" class="w200" :disabled="true"></el-input>
					<el-button type="primary" @click="Select()">选择</el-button>
				</el-form-item>
				<el-form-item label="编码" prop="code">
					<el-input v-model="dataForm.code" placeholder="编码"></el-input>
				</el-form-item>
				<el-form-item label="名称" prop="name">
					<el-input v-model="dataForm.name" placeholder="名称"></el-input>
				</el-form-item>
				<el-form-item label="序号" prop="sort">
					<el-input v-model="dataForm.sort" placeholder="序号" onkeyup="value=value.toUpperCase().replace(/[^\d]/g,'')"></el-input>
				</el-form-item>
				<el-form-item label="状态" size="mini" prop="status">
					<el-radio-group v-model="dataForm.status">
						<el-radio :label="0">启用</el-radio>
						<el-radio :label="1">禁用</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="扩展字段1" prop="ext1">
					<el-input v-model="dataForm.ext1" placeholder="扩展字段1"></el-input>
				</el-form-item>
				<el-form-item label="扩展字段2" prop="ext2">
					<el-input v-model="dataForm.ext2" placeholder="扩展字段2"></el-input>
				</el-form-item>
				<el-form-item label="扩展字段3" prop="ext3">
					<el-input v-model="dataForm.ext3" placeholder="扩展字段3"></el-input>
				</el-form-item>
				<el-form-item label="备注" prop="note">
					<el-input v-model="dataForm.note" placeholder="备注"></el-input>
				</el-form-item>
			</el-form>
			<span slot="footer" class="dialog-footer">
        <el-button @click="addvisible = false">取消</el-button>
        <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
      </span>
		</el-dialog>

		<el-dialog title="修改" :close-on-click-modal="false" :visible.sync="editvisible" width="760px">
			<el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="160px" class="w-80">
				<el-form-item label="父ID" prop="parentId">
					<el-input v-model="parentIdname" class="w200" :disabled="true"></el-input>
					<el-button type="primary" @click="Select()">选择</el-button>
				</el-form-item>
				<el-form-item label="编码" prop="code">
					<el-input v-model="dataForm.code" placeholder="编码"></el-input>
				</el-form-item>
				<el-form-item label="名称" prop="name">
					<el-input v-model="dataForm.name" placeholder="名称"></el-input>
				</el-form-item>
				<el-form-item label="序号" prop="sort">
					<el-input v-model="dataForm.sort" placeholder="序号" onkeyup="value=value.toUpperCase().replace(/[^\d]/g,'')"></el-input>
				</el-form-item>
				<el-form-item label="状态" size="mini" prop="status">
					<el-radio-group v-model="dataForm.status">
						<el-radio :label="0">启用</el-radio>
						<el-radio :label="1">禁用</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="扩展字段1" prop="ext1">
					<el-input v-model="dataForm.ext1" placeholder="扩展字段1"></el-input>
				</el-form-item>
				<el-form-item label="扩展字段2" prop="ext2">
					<el-input v-model="dataForm.ext2" placeholder="扩展字段2"></el-input>
				</el-form-item>
				<el-form-item label="扩展字段3" prop="ext3">
					<el-input v-model="dataForm.ext3" placeholder="扩展字段3"></el-input>
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
		<el-dialog title="选择" :close-on-click-modal="false" :visible.sync="slectVisible">
			<el-tree :data="idList" @node-click="handleNodeClick"></el-tree>
			<span slot="footer" class="dialog-footer">
	        	<el-button @click="slectVisible = false">取消</el-button>
	        	<el-button type="primary" @click="slectSubmit()">确定</el-button>
	      	</span>
		</el-dialog>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				condition: {
					name: '',
				},
				dataList: [],
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false,
				dataListSelections: [],
				addOrUpdateVisible: false,
				addvisible: false,
				editvisible: false,
				slectVisible: false,
				roleList: [],
				idList: [],
				Pdata: [],
				showlist: {
					id: true,
					parentId: true,
					code: true,
					name: true,
					sort: true,
					status: true,
					ext1: true,
					ext2: true,
					ext3: true,
					note: true,
					createUser: true,
					createTime: true,
					updateUser: true,
					updateTime: true
				},
				dataForm: {
					id: 0,
					parentId: 0,
					code: '',
					name: '',
					sort: '',
					status: 0,
					ext1: '',
					ext2: '',
					ext3: '',
					note: ''
				},
				parentIdname: '',
				dataRule: {
					code: [{
						required: true,
						message: '编码不能为空',
						trigger: 'blur'
					}],
					name: [{
						required: true,
						message: '名称不能为空',
						trigger: 'blur'
					}]
				}
			};
		},
		activated() {
			this.getDataList();
		},
		methods: {
			searchHndel() {
				this.pageIndex = 1
				this.pageSize = 10
				this.getDataList()
			},
			searchReset() {
				this.condition.name = ''
			},
			// 获取数据列表
			getDataList() {
				this.dataListLoading = true;
				this.$http({
					url: this.$http.adornUrl("/sale/saleDict/list"),
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
			//选择
			Select() {
				this.slectVisible = true
				this.$http({
					url: this.$http.adornUrl('/sale/saleDict/findTreeByParentId'),
					method: "get",
					params: this.$http.adornParams({
						parentId: 0
					})
				}).then(({
					data
				}) => {
					this.idList = data.dictTree
				});
			},
			//新增
			addHandle() {
				this.addvisible = true;
				if(this.$refs.dataForm) {
					this.$refs.dataForm.resetFields();
				}
				this.parentIdname = ''
				delete this.dataForm.id;
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
				if(this.$refs.dataForm) {
					this.$refs.dataForm.resetFields();
				}
				this.editvisible = true;
				this.dataForm.parentId = this.dataListSelections[0].parentId
				//获取父ID名字
				if(this.dataForm.parentId == 0) {
					this.parentIdname = '字典'
				} else {
					this.$http({
						url: this.$http.adornUrl(`/sale/saleDict/info/${this.dataForm.parentId}`),
						method: "get",
						params: this.$http.adornParams()
					}).then(({
						data
					}) => {
						this.parentIdname = data.dict.name
					});
				}
				//获取内容
				let id = this.dataListSelections[0].id
				this.$http({
					url: this.$http.adornUrl(`/sale/saleDict/info/${id}`),
					method: "get",
					params: this.$http.adornParams()
				}).then(({
					data
				}) => {
					this.dataForm = data.dict;
				});

			},
			handleNodeClick(data) {
				this.Pdata = data
			},
			//确定父ID
			slectSubmit() {
				this.dataForm.parentId = this.Pdata.value
				this.parentIdname = this.Pdata.label
				this.slectVisible = false
			},
			//表单提交
			dataFormSubmit() {
				this.$refs["dataForm"].validate(valid => {
					if(valid) {
						this.$http({
							url: this.$http.adornUrl(
								`/sale/saleDict/${!this.dataForm.id ? "save" : "update"}`
							),
							method: "post",
							data: this.$http.adornData({
								id: this.dataForm.id || undefined,
								parentId: this.dataForm.parentId,
								code: this.dataForm.code,
								name: this.dataForm.name,
								sort: this.dataForm.sort,
								status: this.dataForm.status,
								ext1: this.dataForm.ext1,
								ext2: this.dataForm.ext2,
								ext3: this.dataForm.ext3,
								note: this.dataForm.note
							})
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
							ids.push(e.id)
						});
						var formData = new FormData();
						formData.append("ids", ids);
						this.$http.post(this.$http.adornUrl("/sale/saleDict/delete"), formData)
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